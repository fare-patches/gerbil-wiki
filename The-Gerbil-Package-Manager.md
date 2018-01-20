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
- has a gerbil.pkg file; plist should nominally contain `package:` and `depend:`.
  - `package: <symbol>` should declare your common package prefix, and will apply to
    all your sources recursively.
  - `depend: <list>` should list all package dependencies.
- has a build.ss script that implements the meta, spec, deps, and compile actions.
  - the meta action should return an sexpr list of all the actions supported by the script
  - the spec action should return an sexpr containing the std/make build spec used to
    build the package; the package manager uses that to clean the package.
  - the deps action should build the dependency graph for the package
  - the compile action should build the package, assuming the dependecy graph has
    been build
  - the scipt should also have a default action that does deps and compile, in order
    to support installation by git clone and `M-x gerbil-build` for development.

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
