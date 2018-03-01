This is a quick guide to help you with your first FFI steps with Gerbil.

The first thing to note is that FFI in Gerbil is actually delegated to
Gambit, where the mechanism to interface with C is known as the
[`C-interface`](http://www.iro.umontreal.ca/~gambit/doc/gambit.html#C_002dinterface).

We'll start our foray with a basic example: we are going to query for
the version of `glibc` on the current machine. The C code that achieves this:

```
#include <stdio.h>
#include <gnu/libc-version.h>
int main (void) { puts (gnu_get_libc_version ()); return 0; }
```

We need to write a file module that will define and export an
identifier `get-glibc-version`. Subsequently, we'll import that
identifier and use it in the Gerbil runtime.

```
# Create a gerbil.pkg file for our project
$ cat > gerbil.pkg <<EOF
(package: myuser)
EOF
$ cat > libc-version.ss <<EOF
(export get-glibc-version)
(extern get-glibc-version)
(begin-foreign
  (namespace ("myuser/libc-version#" get-glibc-version))
  (c-declare "#include <stdio.h>")
  (c-declare "#include  <gnu/libc-version.h>")
  (define get-glibc-version (c-lambda () char-string "gnu_get_libc_version")))
EOF
```

File modules take their name from the including file, so this module
is named `libc-version` and uses `myuser/libc-version#` as the
namespace prefix. The package is specified with a `package:
package-path` declaration at the top of a module, or separately in a
file called `gerbil.pkg`. It effects the namespace of the module and
placement of compiled code. By default library modules are looked up
in the `$GERBIL_HOME/lib` and `~/.gerbil/lib` directories.


To feed code straight to the Gambit compiler from Gerbil we use the
`begin-foreign` special form. We namespace the identifier with the
package and module identifiers so as not to pollute the top-level
context.

We compile the module.

```
$ gxc libc-version.ss
```
We're now ready to use the module.

```
$ gxi
> (import :myuser/libc-version)
> (get-glibc-version)
"2.26"
```
