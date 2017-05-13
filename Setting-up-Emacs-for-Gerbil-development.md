Gerbil comes with a custom editing mode, which extends scheme-mode with font-lock and indentation for Gerbil code: [gerbil.el](/vyzo/gerbil/blob/master/etc/gerbil.el).

You can add it to your autoload path (eg by linking in `$HOME/.emacs.d`) and adding this to your .emacs:
```
(autoload 'gerbil-mode "gerbil" "Gerbil editing mode." t)
```

You should further utilize Gambit's inferior mode, as it offers debugger integration with sources on emacs.
It lives in [$GAMBIT_HOME/share/emacs/site-lisp/gambit.el](/gambit/gambit/blob/master/misc/gambit.el).

You can add it to your autoload path and then add this to your `.emacs`:
```
(require 'gambit)
(add-hook 'inferior-scheme-mode-hook 'gambit-inferior-mode)
```

You can then make `gxi` your scheme program by setting `scheme-program-name`:
```
(defvar gsi-options " -:tE8,f8,-8,h2097152")    ; some sensible options for gsi
(defvar gerbil-program-name
  (concat (expand-file-name "~/gerbil/bin/gxi") ; Set this for your GERBIL_HOME
          gsi-options))
(setq scheme-program-name gerbil-program-name)
```

And you can now run Gerbil with `M-x run-scheme`.
