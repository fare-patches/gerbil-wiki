Building complex libraries and executables by invoking `gxc` quickly gets
tedious. When you reach that point of complexity when you need a build tool,
you can use the `std/make` library module which provides a modest build tool
that can handle reasonably complex project building.

### The project source code

For illustration purposes, we'll make a hello world library module and an executable
that uses it.

```
$ cat gerbil.pkg
(package: example)

$ cat util.ss
(export #t)
(def (hello who)
  (displayln "hello, " who))

$ cat hello.ss
(import :example/util)
(export main)

(def (main . args)
  (for-each hello args))

```

### The build script

This is a full fledged build script that can handle building our library and
executable. It also supports dependency tracking for compiling in the correct
order and incremental compilation that only compiles when relevant source
modules are newer than compiled artifacts.

```
$ cat build.ss
#!/usr/bin/env gxi

(def build-spec
  '("util"
    (exe: "hello")))

(def srcdir
  (path-normalize (path-directory (this-source-file))))

(def (main . args)
  (match args
    (["deps"]
     (cons-load-path srcdir)
     (let (build-deps (make-depgraph/spec build-spec))
       (call-with-output-file "build-deps" (cut write build-deps <>))))
    ([]
     (let (depgraph (call-with-input-file "build-deps" read))
       (make srcdir: srcdir
             bindir: srcdir
             optimize: #t
             debug: 'env
             static: #t
             depgraph: depgraph
             prefix: "example"
             build-spec)))))
```

To build our project:
```
$ chmod +x build.ss
$ ./build.ss deps
$ ./build.ss
```

After the initial dependency graph generation, we can build during development
by reusing the dependency graph and simply invoking `./build.ss`. You only need
to generate a new depency graph if your import sets change.

### The Standard Build Script Template

There is a standard build script definition macro in `:std/build-script`,
which generates a `main` function for package build scripts suitable for
packages installable through `gxpkg`.

Using the template would reduce the example build script to this:
```
$ cat build.ss
#!/usr/bin/gxi
(import :std/build-script)
(defbuild-script
  '("util"
    (exe: "hello")))
```

Note that the template requires a `gerbil.pkg` file in the same directory
and that it doesn't allow you to specify a `bindir`. Binaries are placed
in `$GERBIL_PATH/bin` which defaults to `~/.gerbil/bin`.
