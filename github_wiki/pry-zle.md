*Fo' shizzle, ma' pryzzle.*

Pry's default terminal interaction is via Readline. As we start to want more functionality, this is going to prove an obstacle.

## Readline

Readline has never been that great:

- Look at the [vicious hack](https://github.com/tpope/pry-editline/blob/master/lib/pry-editline.rb#L11) used to add a binding into it. I don't see the API to simply bind a key. For example, `Readline.vi_editing_mode` is just C calls.
- Writing actual extensions is harder. I don't know how we could define a custom function that libreadline could call. To get a custom function when inside bash, you do like: `bind -x * '"\ep":"foo"'` and then the function has read/write vars `$READLINE_LINE` and `$READLINE_POS`, but that's about it. Contrast that with the [expressiveness zle funcs](http://linux.die.net/man/1/zshzle).
- It doesn't do multiline stuff. Compare the Bash handling of a multi-line command input + recall versus the Zsh equivalent.  Zsh keeps it splayed out on several lines, but Bash canonicalizes it as a one-liner.  Our Pry experience is even worse on this one, as it chops multiline input up into individual history entries, so recalling basically requires `edit` (and then you've lost REPL powers).
- It has no "Menu" output like `zle -M`. To see the widget I'm talking about in action in zsh, tabcomplete something. It will show a temporary menu hanging below the prompt, which goes away any time you tabcomplete something else or when you hit Enter.
- Bad OS X support by default (e.g. the libedit backend doesn't support emacs + vi modes)
- [Requires `libreadline-dev` to be present](https://mallibone.wordpress.com/2012/02/21/fixing-ruby-pryreadline-under-ubuntu/) when Ruby is built.

## Coolline

Coolline is interesting, but very incomplete.

- No easily-made "Sub-modes". E.g. if you wanted to do vi's `daw` it would require making `d` swap in a different `:handlers` value.
- No undo.
- No repeat.
- No registers (~copy & paste)
- Hard-codes terminal sequences (e.g., no $TERM handling; no support for degraded functionality)
- Also does no multiline editing.
- Also has nothing like `zle -M`

We could get to the point where we have all of these things, and it'd be nice because it would all be in-house and malleable, but it's so much work.

I really think the solution is:

## The Zsh Line Editor

The software is the best command-line interaction I've dealt with.

I think using it would be smoothest if we had a 2nd I/O channel for Pry, so that the normal `$stdout` would work, but that we could also query it for completion, `show-doc` output, `show-source`, etc.


Another option entirely:

## LeoNerd's stuff

Several of [his projects](http://home.leonerd.org.uk/code/) are relevant.
- [`libtermkey`](http://home.leonerd.org.uk/code/libtermkey/) - Keyboard input
- [`libtickit`](http://home.leonerd.org.uk/code/libtickit/) - UI (input via libtermkey)
- …several others.

The advantage here is that he's committed and very knowledgeable about terminals.

<!-- vim:ft=markdown
-->
