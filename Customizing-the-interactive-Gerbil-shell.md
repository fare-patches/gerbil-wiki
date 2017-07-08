Whenever gxi is run interactively, after loading and initializing the runtime and expander, it loads the interactive initialization file `$GERBIL_LIB/init.ss` (source: [init.ss](/vyzo/gerbil/blob/master/src/gerbil/interactive/init.ss)), which greets the user, imports the Gambit prelude, and provides some features only available in interactive setting, like the `reload` macro.

Then it checks for a user specific interactive initialization file. If the file `~/.gerbil/init.ss` exists, then it is included in the top (interaction) environment. This allows you to customize your interactive Gerbil shell, but note that the file is _not_ sourced when executing scripts non-interactively.

Here is a small example `~/.gerbil/init.ss` that provides useful functionality:
```
;;; -*- Gerbil -*-
;; only useful if you intend to do Gerbil system development
;; (add-load-path (path-expand "src" (_gx#gerbil-home)))

;; add your work environment's source tree to the load path
;; (add-load-path "/path/to/your/gerbil/project/lib" "/path/to/your/gerbil/project/src")

;; macro-expand an expression and pretty print the result -- useful for debugging macros
(defsyntax (@expand stx)
  (syntax-case stx ()
    ((_ expr)
     (with-syntax ((expr* (gx#core-expand* #'expr)))
       (pretty-print (syntax->datum #'expr*))
       #'(quote-syntax expr*)))))

;; single-step macro-expand an expression and pretty print the result
;; useful for debugging intermediate expansion from complex macros
(defsyntax (@expand1 stx)
  (syntax-case stx ()
    ((_ expr)
     (with-syntax ((expr* (gx#core-expand1 #'expr)))
       (pretty-print (syntax->datum #'expr*))
       #'(quote-syntax expr*)))))
```