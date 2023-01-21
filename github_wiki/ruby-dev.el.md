
## [Mon-Ouie/ruby-dev.el](https://github.com/Mon-Ouie/ruby-dev.el)!

    cd ~/.emacs.d
    git clone https://github.com/Mon-Ouie/ruby-dev.el
    cat >> init.el <<EOT
    (add-to-list 'load-path "~/.emacs.d/ruby-dev.el" )
    (autoload 'turn-on-ruby-dev "ruby-dev" nil t)
    (add-hook 'ruby-mode-hook 'turn-on-ruby-dev)
    EOT
    gem install pry yard

With those config lines above, whenever you edit some Ruby code (or `M-x
turn-on-ruby-dev`), it will enable the `ruby-dev` minor mode.

Note that it is very new, so it might have some rough edges.

Evaluating Code
---------------

Starting simple, there's `C-c C-s`. Press that then say: `5+3`.

In the minibuffer, at the bottom, you'll see an `8`.

As soon as you do any code evaluation, a Ruby process will start inside Emacs
with which the editor can communicate (via JSON).

Next, type:

    (("le.ved-ybur"*50).reverse)

Then hit `C-c C-e` (or `C-x C-e` ).

The parens are here because this will evaluate the "Ruby sexp" (the unit of
which is visible by pressing `C-M-b` and `C-M-f` to hop around.)

Also possible is `C-c C-b` to evaluate the whole buffer, or `C-c C-r` to
evaluate the marked region (e.g., marked with `C-Space`).

Entering the Pry REPL
---------------------

Use `C-c C-i` (Ctrl+c, Tab) to enter a REPL session. _(rking is having an issue
with this where the buffer comes up empty. If you replicate, please let us
know on Freenode #pry.)_

Notice how the context of the evaluations from before are present in this
session. This is because the process, `ruby-dev`, is the one that runs
`Pry.start`. If for some reason you want to restart this process, you can run
`M-x ruby-dev`

Hyperlinked `ri` Docs
---------------------

`C-c C-d` to get an navigable `ri` interface[!](https://github.com/Mon-Ouie/ruby-dev.el/blob/master/ruby-dev-doc.el)


Further documentation
---------------------

To see all keybindings, do:

    C-x C-h

To see info about the internal functions, try:

    C-h a ruby-dev RET

