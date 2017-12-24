Whenever gxi is run interactively, after loading and initializing the
runtime and expander, it loads the interactive initialization file
`$GERBIL_HOME/lib/init.ss` (source: [init.ss](/vyzo/gerbil/blob/master/src/gerbil/interactive/init.ss)),
which greets the user, imports the Gambit prelude, and provides some
features only available in interactive setting, like the `reload`
macro.

Then it checks for a user specific interactive initialization file. If
the file `~/.gerbil/init.ss` exists, then it is included in the top
(interaction) environment. This allows you to customize your
interactive Gerbil shell, but note that the file is _not_ sourced when
executing scripts non-interactively.

Here is a small example `~/.gerbil/init.ss` that provides useful functionality:
```
;;; -*- Gerbil -*-

;; add your work environment's source tree to the load path
;; (add-load-path "/path/to/your/gerbil/project/src")

;; only useful if you intend to do Gerbil system development
;; (add-load-path (path-expand "src" (getenv "GERBIL_HOME")))

;; import your personal gerbil libraries
;; (import :my/stuff)

```
