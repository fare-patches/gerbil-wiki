Gerbil comes with a command line package manager, `gxpkg`, which allows you
to install and manage packages distributed through github.

## Synopsis

```
gxpkg install pkg ...
gxpkg update pkg ...
gxpkg uninstall pkg ...
gxpkg link pkg src
gxpkg unlink pkg ...
gxpkg build pkg ...
gxpkg list
gxpkg retag
```

## Gerbil Packages

Any github repo can serve a Gerbil package, provided that
- has a `gerbil.pkg` file; plist should nominally contain `package:` and `depend:`.
  - `package: <symbol>` should declare your common package prefix, and will apply to
    all your sources recursively.
  - `depend: <list>` should list all package dependencies.
- has a `build.ss` script that implements the meta, spec, deps, and compile actions.
  - the meta action should return an sexpr list of all the actions supported by the script.
  - the spec action should return an sexpr list containing the std/make build specs used to
    build the package; the package manager uses that to clean.
  - the deps action should build the dependency graph for the package.
  - the compile action should `make` the package, assuming the dependecy graph has
    been built.
  - the script should also have a default action that does deps and compile, in order
    to support installation by git clone and `M-x gerbil-build` for development.

You can use `:std/build-script` to get a template script definition from the package
build-spec.

See [gerbil-utils](https://github.com/fare/gerbil-utils) for an example package.

## Examples

To install fare's gerbil-utils package:
```
gxpkg install github.com/fare/gerbil-utils
```

To link a local development package (here vyzo's gerbil-aws package):
```
gxpkg link github.com/vyzo/gerbil-aws gerbil-aws
```

To list all installed (or linked) packages:
```
gxpkg list
```

To update all packages:
```
gxpkg update all
```

To rebuild a package and its transitive dependencies:
```
gxpkg build github.com/fare/gerbil-utils
```

To rebuild all packages:
```
gxpkg build all
```

## A Word of Caution

The build script is not sandboxed; it runs with user priveledges
and it is an arbitrary script. We plan to address this in the future
by creating a restricted sandbox language for package build scripts.

For now, you should only install packages from sources you trust.
That's ok for the early days of the Gerbil Clan, as we don't have
a trust problem yet.
