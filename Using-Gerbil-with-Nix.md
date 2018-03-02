You can use Gerbil on NixOS, or with the Nix package manager without NixOS,
and it will guarantee you that Gerbil is correctly built, in a deterministic way,
on top of a properly built Gambit with all the correct options.

Satisfactory Nix recipes for Gerbil were merged in nixpkgs starting in September 2017,
and propagated to other branches from there.
Updates will be upstreamed regularly, but if you care for the latest recipe,
see my nixpkgs fork at < http://github.com/fare-patches/nixpkgs >.
Make sure your `NIX_PATH` variable defines `nixpkgs=/path/to/checkout/of/nixpkgs`
and to consistently use `nix-env -f '<nixpkgs>'` or `nix-shell '<nixpkgs>'`, etc.
You may abstract this usage in shell (or gerbil) functions as appropriate.
You can install it with `nix-env -f '<nixpkgs>' -iA gerbil` and then get a REPL with `gxi`.

The recipe was tested on Linux on i686 and x86_64.
It should just work on macOS, on other Linux architectures, and maybe even on Windows,
though it may or may not require further hacking in some cases.
Please report successes and failures to us.
Note that static linking of Gerbil programs is not currently well supported in Nix;
fixing that will require updating the recipes for relevant libraries.

To compile using `gxc` should work fine from within Nix recipes
if you add `gerbil`, `gambit` and all the libraries you use to your package's dependencies.
To use `gxc` from the command-line outside a `nix-shell`, you may use a variant of the script below,
that I call `gerbil-nix-env.sh`, and that I typically place at the top level directory
of the software I write in Gerbil:

```
# gerbil-nix-env.sh -*- Shell -*-
# Copyright 2017 Francois-Rene Rideau <fare@tunes.org>
# This file is published under both LGPLv2.1 and Apache 2.0 licenses.
#
# Source this file into your shell environment to define important variables
# that let you compile with gerbil from within your interactive Nix environment.
#   source .../gerbil-nix-env.sh

if [ -n "${BASH_VERSION-}" ] ; then
    this=${BASH_SOURCE[0]}
elif [ -n "${ZSH_VERSION-}" ] ; then
    this=$0
elif [ -n "$this" ] ; then
    # For this file to work on shells other than bash and zsh, e.g. dash,
    # the caller must define the variable $this the location of a file
    # in the same directory as gerbil-nix-env.sh.
    # for instance my-gxi in this same directory uses:
    #    this=$0
    :
else
    echo "Unknown shell and \$this not defined" ; unset this ; set -u ; return 1
fi


###### USER-EDITABLE SETTINGS #####

# This setting assumes that this file is copied to or symlinked from the
# top directory for your Gerbil source.
# If that is not the case, adjust this variable accordingly
export MY_GERBIL_SRC=$(realpath "$(dirname "$this")")

# If you want other library directories in your loadpath, adjust this, too:
export GERBIL_LOADPATH=$MY_GERBIL_SRC

###### END OF USER-EDITABLE SETTINGS #####

# Enable more debugging, plus all I/O and source UTF-8 by default
export GAMBOPT=t8,f8,-8,dRr

export GERBIL_HOME=$(dirname "$(dirname "$(realpath "$(which gxc)")")")

# Get the flags for compiling and linking against openssl and other libraries.
eval "$(nix-shell '<nixpkgs>' --pure --attr gerbil --command \
  'echo "export \
     NIX_SHELL_PATH=\"$PATH\" \
     NIX_LDFLAGS=\"$NIX_LDFLAGS\" \
     NIX_BINTOOLS=\"$NIX_BINTOOLS\" \
     NIX_CC=\"$NIX_CC\" \
     NIX_CFLAGS_COMPILE=\"$NIX_CFLAGS_COMPILE\""')"

: ${ORIG_PATH:=$PATH}
export ORIG_PATH
export PATH="$NIX_SHELL_PATH:$ORIG_PATH"

# This enables the NIX wrapper
target=$("${NIX_CC}/bin/cc" -v 2>&1 | sed '/^Target: /!d ; s/^Target: // ; s/[^a-zA-Z0-9_]/_/g')
eval "export NIX_CC_WRAPPER_${target}_TARGET_HOST=1"
eval "export NIX_BINTOOLS_WRAPPER_${target}_TARGET_HOST=1"
```
