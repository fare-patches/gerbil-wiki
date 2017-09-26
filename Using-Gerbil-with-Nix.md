You can use Gerbil on NixOS, or with the Nix package manager without NixOS, and it will guarantee you that Gerbil is correctly built, in a deterministic way, on top of a properly built Gambit with all the correct options.

The Gerbil recipe for Nix is already in nixpkgs-unstable as of 2017-09. You can install it with `nix-env -i gerbil` and then get a REPL with `gxi`. The recipe was tested on Linux. It should just work on macOS, but wasn't tested. It may or may not work on Windows, though it may or may not require a little bit of hacking. Please report successes and failures to us.

To compile using `gxc` should work fine from within Nix recipes
if you add `gerbil`, `gambit` and all the libraries you use to your package's dependencies.
To use `gxc` from the command-line outside a `nix-shell`, you may use a variant of the script below,
that I all `gerbil-nix-env.sh`:

```
# Source this file into your shell environment to define important variables
# that let you compile with gerbil from within your interactive Nix environment.
#   source .../gerbil-nix-env.sh

if [[ -n "${BASH_VERSION-}" ]] ; then
    this=${BASH_SOURCE[0]}
elif [[ -n "${ZSH_VERSION-}" ]] ; then
    this=$0
else
    echo "UNKNOWN SHELL" ; unset this ; set -u ; return 1
fi

###### USER-EDITABLE SETTINGS #####

# This setting assumes that this file is in the top directory for your Gerbil source.
# If that is not the case, adjust this variable accordingly
export MY_GERBIL_SRC=$(readlink -f $(dirname $this))

# If you want other library directories in your loadpath, adjust this, too:
export GERBIL_LOADPATH=$MY_GERBIL_SRC

# If you're not running on Linux x64, adjust this:
export NIX_CC_WRAPPER_x86_64_unknown_linux_gnu_TARGET_HOST=1

###### END OF USER-EDITABLE SETTINGS #####


export GERBIL_HOME=$(dirname $(dirname $(readlink -f $(which gxc))))

#export GSIOPTIONS="-:tE8,f8,-8,h2097152"
# set it through GAMBOPT for compiled unicode
unset GSIOPTIONS
export GAMBOPT=t8,f8,-8,dRr

# Get the flags for compiling and linking against openssl and other libraries.
eval $(nix-shell '<nixpkgs>' --pure --attr gerbil --command \
  'echo "export \
     NIX_SHELL_PATH=\"$PATH\" \
     NIX_LDFLAGS=\"$NIX_LDFLAGS\" \
     NIX_CC=\"$NIX_CC\" \
     NIX_CFLAGS_COMPILE=\"$NIX_CFLAGS_COMPILE\""')

: ${ORIG_PATH:=$PATH}
export ORIG_PATH
export PATH=$NIX_SHELL_PATH:$ORIG_PATH
```
