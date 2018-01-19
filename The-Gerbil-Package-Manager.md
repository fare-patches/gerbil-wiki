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
- it has a gerbil.pkg file; dependencies are specified with a `depend:` list in the plist.
- it has a build.ss script that implements the spec, deps, and compile actions.

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
