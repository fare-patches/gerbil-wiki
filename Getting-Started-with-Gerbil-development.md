This is a quick start guide to setting up your Gerbil development environment, starting from scratch.

### Install Gambit
* Clone the repo from [github](https://github.com/gambit/gambit)
* Follow the instructions from `gambit/INSTALL.txt` to install configure and install gambit, as the process for building from source tends to change frequently.

I usually configure Gambit with the following incantation:
```
 ./configure --prefix=/usr/local/gambit \
             --enable-single-host --enable-c-opt --enable-gcc-opts \
             --enable-multiple-versions --enable-shared --enable-openssl
```

With this configuration, you need to add `$GAMBIT_PREFIX/current/bin` to your `PATH` and `$GAMBIT_PREFIX/current/lib` to your `LD_LIBRARY_PATH`.

If you intend to build applications for servers, then you should use the following configuration:
```
./configure --prefix=/usr/local/gambit \
             --enable-single-host --enable-c-opt --enable-gcc-opts \
             --enable-multiple-versions --enable-openssl --enable-poll
```
This removes `--enable-shared`, which will build gambit without shared libraries and thus result in static linkage of Gambit in executables.
It also adds `--enable-poll` to use poll and allow scaling to arbitrary numbers of file descriptors [WIP in Gambit].

I have the following in my `.bashrc`:
```
GAMBIT=/usr/local/gambit/current
add_path $GAMBIT/bin
add_ldpath $GAMBIT/lib
```

with the helper functions defined as:
```
add_path() {
    if [[ ! "$PATH" =~ $1 ]]; then
        export PATH="$PATH:$1"
    fi
}

add_ldpath() {
    if [ -z "$LD_LIBRARY_PATH" ]; then
        export LD_LIBRARY_PATH="$1"
    elif [[ ! "$LD_LIBRARY_PATH" =~ $1 ]]; then
        export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$1"
    fi
}

```

### Install Gerbil
Clone the repo from [github](https://github.com/vyzo/gerbil) and build:
```
$ cd gerbil/src
$ ./build.sh
```

In order to have a usable installation, you need to export `GERBIL_HOME` and add `$GERBIL_HOME/bin` to your `PATH`. I have the following in my `.bashrc`:
```
export GERBIL_HOME=$HOME/gerbil
add_path $GERBIL_HOME/bin
```

### Write some code
You can get started right away and write a script, but let's do a simple 
compiled library module and an executable as it is more relevant for real
world development.

First create your workspace -- I recommend you use a top package for your libs
so that you don't have namespace conflicts.
So let's make our project with you using my-user as the top package name, and
your source live in my-gerbil. You should of course pick something more
representative  for your top package namespace, like your github user id.

So, let's make a simple library module:
```
$ mkdir -p my-gerbil/src/my-user
$ cd my-gerbil/src/my-user

$ cat > mylib.ss <<EOF
package: my-user
(export #t) ; export all symbols
(def (hello who)
 (displayln "hello " who))
EOF
```

Now let's compile it. By default, gxc will place compiler artefacts in `$GERBIL_HOME/lib`. 
You can change this with the `-d` option, but then you'll have to add your libdir to `GERBIL_LOADPATH`.

```

$ gxc mylib.ss
```

You now have a compiled module, which you can use in the interpreter:
```
$ gxi
> (import :my-user/mylib)
> (hello "world")
hello world
```

Next let's make an executable:
```
$ cat > mybin.ss <<EOF
package: my-user
(import :my-user/mylib)
(export main)
(def (main who)
 (hello who))
EOF
```
and let's compile it and run it:
```
$ gxc -exe -o mybin mybin.ss
$ ./mybin world
hello world
```
Note that this is a dynamically linked executable, the module has been
compiled dynamically in the gerbil libdir and the executable is a stub
that loads it and executes main, which means that your `GERBIL_HOME`
(and `GERBIL_LOADPATH` if you are putting your artefacts in a different
place, like `my-gerbil/lib`) must be set.

You can also compile a statically linked executable, which can work without
a local gerbil environment:
```
$ gxc -static mylib.ss  # compile dependent library statically first
$ gxc -static -exe -o mybin-static mybin.ss
$ ./mybin-static world
hello world
```

The advantage of static executables is that they can work without a local
Gerbil installation, which makes them suitable for binary distribution.
They also start a little faster, as there is no dynamic module loading at runtime.
In addition, because all dependencies from the stdlib are compiled in together, you
can apply global declarations like `(declare (not safe))` to the whole program, which
can result in significant performance gains.

The downside is increased size and the limitation that the executable can't
use the expander or the compiler, as the meta parts of the Gerbil runtime are
not linked in.
