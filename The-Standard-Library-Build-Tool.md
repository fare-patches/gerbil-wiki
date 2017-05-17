Building complex libraries and executables by invoking `gxc` quickly gets
tedious. When you reach that point of complexity when you need a build tool,
you can use the `std/make` library module which provides a modest build tool
that can handle reasonably complex project building.

### Building Libraries
Let's take the example library from the guide:
```
$ cat example/util.ss 
package: example
(export with-display-exception)
(extern (display-exception display-exception))
(def (with-display-exception thunk)
  (with-catch (lambda (e) (display-exception e (current-error-port)) e)
    thunk))
```

The following script can be used to build the little
example library:
```
$ cat > build.ss <<EOF
#!/usr/bin/env gxi-script
(import :std/make)
(let (srcdir (path-directory (this-source-file)))
  (make srcdir: srcdir
        '("example/util")))
EOF
$ chmod +x build.ss

```

You can now build the library by invoking `build.ss` and have it
installed by default into `$GERBIL_HOME/lib` -- the location can
vary by specifying a `libdir:` option to `make`.
```
$ ./build.ss
... compile example/util
$ gxi
> (import :example/util)
> (with-display-exception (lambda () (error "this-is-an-error")))
this-is-an-error
=> #<error-exception #4>
```

### Building Executables
Executables can also be built -- the following extends the buildspec
for building an executable for our `hello` program:
```
$ cat example/hello.ss 
package: example
(export main)
(def (main . args)
  (displayln "hello world"))


$ mkdir bin # to place our executables
$ cat > build.ss <<EOF
#!/usr/bin/env gxi-script
(import :std/make)
(let (srcdir (path-directory (this-source-file)))
  (make srcdir: srcdir
        bindir: (path-expand "bin" srcdir) 
        '("example/util"
          (exe: "example/hello"))))
EOF
$ chmod +x build.ss
$ ./build.ss
... compile example/hello
... compile exe example/hello
$ ./bin/hello 
hello world
```

### Static Executables
Static executables are also supported with the `static-exe:` buildspec.
Note that if you have any dependencies in your project you should also
specify the `static:` parameter to `make` so that your library modules
are also compiled for static linkage.
The following `build.ss` can be used to build hello statically:
```
$ cat > build.ss <<EOF
#!/usr/bin/env gxi-script
(import :std/make)
(let (srcdir (path-directory (this-source-file)))
  (make srcdir: srcdir
        bindir: (path-expand "bin" srcdir)
        static: #t
        '("example/util"
          (static-exe: "example/hello"))))
EOF
$ chmod +x build.ss
$ ./build.ss
... compile example/util
... compile example/hello
... compile static exe example/hello
$ ./bin/hello 
hello world

```

### More buildspecs
The build tool can handle quite a few different buildspecs and generate/use
dependency graphs, while by default it only builds if the source is
newer than the artefacts (or any of the latter doesn't exist).
The canonical example for its usage is the stdlib [build.ss](/vyzo/gerbil/blob/master/src/std/build.ss)
script, as the `std/make` library evolved out of that script.
