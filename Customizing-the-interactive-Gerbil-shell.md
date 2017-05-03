Whenever gxi is run interactively, after loading and initializing the runtime and expander, it checks for a user specific interactive initialization file. If the file `~/.gerbil/init.ss` exists, then it is included in the top (interaction) environment. This allows you to customize your interactive Gerbil shell, but note that the file is _not_ sourced when executing scripts non-interactively.

Here is a small example `~/.gerbil/init.ss` that provides useful functionality:
```
;;; -*- Gerbil -*-
;; pleasantly greet the user
(displayln (gerbil-system-version-string))

;; only useful if you intend to do Gerbil system development
;; (add-load-path (path-expand "src" (_gx#gerbil-home)))

;; add your work environment's source tree to the load path
;; (add-load-path "/path/to/your/gerbil/project/src")

;; import the Gambit prelude
(import :gerbil/gambit)

;; reload an interpreted module -- useful for repl testing while developing
(defrules reload () 
  ((_ mod) 
   (begin (begin-syntax (import-module 'mod #t)) (import mod))))

;; macro-expand and pretty print an expression -- useful for debugging macros
(defsyntax (@expand stx)
  (syntax-case stx ()
    ((_ expr)
     (with-syntax ((expr* (gx#core-expand* #'expr)))
       (pretty-print (syntax->datum #'expr*))
       #'(quote-syntax expr*)))))

```