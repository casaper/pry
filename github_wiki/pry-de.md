pry-de
======

No, it's not a pry with sauerkraut: It's the Pry Development Environment, silly!

The Ideas
---------

An editor is a good "home base" for traditional development, because the whole mentality is file-based storage and pre-compile-time editing. There is more information avialable at run-time, so (theoretically) it's a better place to do work. Pry is, as far as I know, the bleeding edge of run-time environments overlaid on top of file-based environments (that is, if you want the full force, try out [Pharo Smalltalk](http://www.pharo-project.org/home) — but Smalltalk is too radical, so people don't generally use it.)

0. The runtime, compiletime, and edittime are all implementation details. It's just time, and the programmer should be free of the boundaries between them.
0. The programmer and user roles are arbitrarily distinguished. There's just a machine and some people who work on it. Each person that touches the system should be able to add their own kind of value, and should be able to upstream that contribution.
0. The tests and implementation are two sides of the same coin. Separating them is a mis-step.

The Task
--------

The key elements seem to be:
- Exception handling that brings you into the code rather than just quitting.
- Interactivity so you can explore the API
- Promotion so you can make temporary scraps become permanent on the
  filesystem, eventually to VCS.

It's about tightening feedback cycles: shortening the time between when you
have a question and when you get the answer.

What We Have
------------

Integration from editor toward REPL:

- emacs through Mon Ouïe's [ruby-dev.el](ruby-dev.el)
- vim:
  * [pry-de's vim file](https://github.com/rking/pry-de/blob/master/vim/ftplugin/ruby_pry.vim)
    - `<leader>bp` to add `require'pry';binding.pry`
    - `bpry` from Insert Mode (to do the same thing) 

Integration from the REPL straight to the filesystem:

- Pry's existing `edit` command:
  * Tabcompletes filenames (git pry with the bond gem installed does this)
  * [demo in showterm](http://showterm.io/ba798d63ac1d24c0db8bf#slow)
- The new `save-source` command (on the [feature/save-source branch](https://github.com/pry/pry/tree/feature/save-source)) 
  * Has some bugs, but is already looking very promising
  * [demo in showterm](http://showterm.io/5dfdc641a558cdb1eabb0#slow)

Integration from the REPL toward the editor:

- vim:
  * [pry-de's vim file](https://github.com/rking/pry-de/blob/master/vim/ftplugin/ruby_pry.vim)
    - `<leader>pry` to read N lines from the tail of the `~/.pry_history`
  * [tslime.vim (for tmux)](https://github.com/kikijump/tslime.vim) (See the ancestry here: [slime.vim (for screen)](http://technotales.wordpress.com/2007/10/03/like-slime-for-vim/).)
      * Note: pry-de should include a vim file to be a better version of this.
      * Also note [slimv.vim](http://www.vim.org/scripts/script.php?script_id=2531), a lisp-based tool.
  * Also: https://github.com/MarcWeber/vim-addon-async#repl-read-print-eval-loop-implementations-are-available-for

Integration from the run-time toward the REPL:

- [better_errors](https://github.com/charliesome/better_errors) - Automatically drop into a REPL on any Rack/Rails errors. See especially: `BetterErrors.use_pry!`

Plus the eponymous Work-in-Progress:

- `pry-de` [repo](http://github.com/rking/pry-de) and [gem
  v0.0.3](http://rubygems.org/gems/pry-de) has only a small handful of things.
  * `,s` == `step` `,n` == `next`, etc.
  * `,loc` shows locals (should become `ls -l`)
  * `,w` == `whereami`
  * Bare `,,` shortens `,c ,s ,n` to `c s n`
  * `,,e` added to the end of a line (like `? Foo.bar ,,e` to act like `edit-method Foo.bar`
    (`,,` because it's a syntax error (thus clearly not Ruby), and easy to type.)
  * See the repo [README.md](http://github.com/rking/pry-de#readme) for details.

What We Need
------------

Integration from editor toward REPL:

- vim:
    - Possibly add [DBGP](http://xdebug.org/docs-dbgp.php) support, then
      [Vdebug](http://www.vim.org/scripts/script.php?script_id=4170) would work.

TODO: Add "Integration… categories"

- Pry's existing `edit` command:
  * Should edit classes as well as methods (and give a good interface for when the
    class is scattered, i.e., reopoened or dynamically created somehow)
  * Somehow be test/spec-aware. Like `edit -t SomeClass` brings you to the
    spec for it. This might require a mapping somewhere. Or maybe it's time to
    start putting tests inline with the implementation?
  * `,e` by itself will have some context-sensitive semantics to complement `edit -c`
- `,r` will act like zsh's `r`: Repeating previous command. (TODO: make this
  work without recursion)
- Rails optimizations
   * `,emodel` ⇒ `edit app/model/#{arg}`
   * `,econtroller`
   * `,a` ⇒ "Alternate", hop from implementation to test/spec.
   * …Otherwise steal everything from [rails-vim](https://github.com/tpope/vim-rails).
   * Note: The newer versions of the `pry-rails` gem have some cool commands:
       - `show-models`
       - `show-routes` (Note especially how it executes instantaneously (compared to the sluggish `rake routes`). This is because working at run-time ≡ winning.)
       - `show-middleware`

- pry-git for git stuff.
  * These commands need more love. Will determine the missing bits soonish.

- pry-theme to homogenize syntax highlighting between REPL and text editor
  (e.g., the vim-default theme). (Done-ish. My new
  [detailed.vim](https://github.com/sharpsaw/vim-dots/blob/master/.vim/colors/detailed.vim) theme is on
  a solid start, but I need to update `pry-theme`s `vim-detailed` to match)

- Should be able to hit a keybinding (`^d`?) and see the results of the
  current expression below the current line. This output would go away the
  next time you hit that keybinding or when you hit Enter.
- Same widget would be good for `?` and `$` output — after you're done with
  it, it just clutters the screen.
- pry-stack_explorer, and pry-exception_explorer to make the runtime
  env more maneuverable.  **(done)**
- pry-vterm_aliases to reduce the need to drop back to the shell **(done)**

<a name="red-green-refactor"/>
The Loop
--------

Making pry-de a reality should center around one, really solid develop
workflow.  What better than the "red/green/refactor" loop?

## Guard loop

0. Use the
[interactor/pry](https://github.com/guard/guard/tree/interactor/pry) feature
of guard.
0. Tinker around at the command line until you get the data you want. E.g.:
`a = 1+2+3 ⇒ 6`
0. Run `new-test Foo monkeys assert_equal #{a}, monkeys([1,2,3])` (TODO: write
this command. It evaluates to: a pretty-formatted version of
`class FooTest; def test_monkeys; assert_equal… end end`)
0. `try-test input; input.inject 0 do |a,e| a += e end` (TODO: write this
command. Since you already set the stage with `new-test`, it knows to define
this as a pretty-formatted `class Foo; def monkeys input; input.inject… end end`)
0. This will show a success for that run.
0. `save-test` (will invoke `save-source Foo` and `save-source FooTest`, both
of which will trigger the existing Guard logic to run those saved `.rb`s and
then the whole suite)
0. `refactor` (Just runs `Pry.config.editor` on `$(git status --porcelain lib | cut -b4-) "$@"`)
0. `git-commit`
0. Repeat until time to lock the laptop.

Notice that you never leave the Guard's pry loop.

- Will probably a method like the Rails console's `reload!`.
- Guard has some odd interaction with readline/pry that I have yet to reliably
  replicate, but it requires a `\`stty echo\`` to see what you're typing.
- Maybe the "ran all tests successfully" step should automatically prompt to
  `refactor` the first time.

See also:
- [CheckL](https://github.com/rpav/CheckL) - A lisp tool. <!-- install sbcl, quicklisp, and (ql:quickload :checkl)" (And an [example project](https://github.com/rpav/fast-io) that uses it (note the `asdf` config stuff)) -->

Web Companion
-------------

pry-de could integrate with something like [pry-mirror](https://github.com/jasonlaster/pry-mirror). This could allow for an easy upgrade of UI, such as hyperlinking, subdividing the screen, and graphics, but it could also have some killer use cases along the lines of rapidly developing views or AJAX code.

Further Research
----------------

- [4 Levels of Liveness
  (.pdf)](http://reference.kfupm.edu.sa/content/i/m/implementing_level_4_liveness_in_declara_56942.pdf)
  - A paper that seems to have taken this even more seriously than I did.
- [IRT](https://github.com/ddnexus/irt), which tries hard, but doesn't quite achieve pry-de levels (most of its time is spent catching IRB up to Pry).
- Must match the speed of test runs in [this SublimeText
  demo](http://youtu.be/05x1Jk4rT1A#t=3m50s). I have no idea how they're going
  so fast; I must be under-using Guard+Spork.

Factor (thanks, epitron!) http://youtu.be/f_0QlhYlS8g [TOWATCH]
- `Ctrl+w` to start the current line in the "Walker", a step/next/etc
  debugger [@5m15s](http://youtu.be/f_0QlhYlS8gk#t=5m15s)
- Everything is hyperlinked, e.g. they define `factorial` using an interesting
  `[a,b]` notation, but within the Walker you can click on it and see its
  defintion.
- `Ctrl+Shift+h` gives help for previous token (note: not working for me IRL, it
  seems) @10m
- [Lengthy description of the language itself, which is really cool, but not
  related to pry-de]
- `Ctrl+r` to profile @51m30
- Pausing at http://youtu.be/f_0QlhYlS8gk#t=1h

Also:
- NoMethodError equivalents do some fuzzy-finding then offer you to pick one
  or defer definition.
- `F2` to refload all files

Smalltalk: [Squeak](http://squeak.org) and its fork
  [Pharo](http://www.pharo-project.org/) ([Pharocasts](http://www.pharo-project.org/documentation/screencasts))

[Common Lisp Exceptions](http://gigamonkeys.com/book/beyond-exception-handling-conditions-and-restarts.html)
<!--
Twey │ rking: You should look at Andrej Bauer's ‘Eff’, too, if you're interested in exception-handling.
Twey │ And/or Connor McBride's ‘Frank’
-->

[light-table](http://www.kickstarter.com/projects/306316578/light-table) - has
  some good ideas
[Tern.JS](http://www.indiegogo.com/projects/tern-intelligent-javascript-editing) - an editor for JS that knows about types and such

<a name="slime"/>
[SLIME vid (150mb .mov)](http://common-lisp.net/project/movies/movies/slime.mov)
- First ~8 minutes: Cumbrous setup of Swank on remote machine, Slime on local machine
- `*`/`**`/`***` (like Pry's `_`/`__`/`_out_[-2]`) # `mplayer slime.mov -ss 8:23`
- 'Output Presentations': Allow references to any value previously output by REPL
- Shortcuts: Get list by hitting `,` on empty line. Includes `change-directory`, `change-package`, `help`, etc. (Like Pry's commands) # `mplayer slime.mov -ss 9:25`
- `C-c C-d d` = `slime-describe-symbol`. Like `show-doc` # `mplayer slime.mov -ss 10:04`
- `C-c C-d h` = `slime-hyperspec-lookup`.  # `mplayer slime.mov -ss 11:58`
- `C-M-x` = `slime-eval-defun`, evaluate 'top-level form' # `mplayer slime.mov -ss 12:45`
- `C-x C-e` = `slime-eval-last-expression` (evaluate form to left of cursor)
- `C-c C-c` Sends top-level form to SLIME, but don't show expression result # `mplayer slime.mov -ss 17:16`
- Slime selector # `mplayer slime.mov -ss 13:49`
    * `?` - help
    * `c` - "SLIME connections buffer" (you can connect to several lisps at
      once. I think that's what this is.)
    * `d` - \*sldb\* (debugger) buffer for the current connection
    * `e` - most recently visited emacs-lisp-mode buffer (jumps back to code; ike pry-de
      `refactor` or `respec`)
    * `i` - "\*inferior-lisp\* buffer" (?)
    * `l` - same as `e` but for lisp-mode instead of emacs-lisp-mode
    * `r` - SLIME REPL
    * `s` - \*slime-scratch\* buffer
    * `t` - "SLIME threads buffer" (?)
    * `v` - "\*slime-events\* buffer" (?)
    * `n` - "Cycle to the next Lisp connection"
    * `m` - "First mrepl-buffer" (?)
- `change-package` (like Pry's `cd`?) # `mplayer slime.mov -ss 14:20`
- Typing function name gives signature for `lambda list` (looks like method signature) # `mplayer slime.mov -ss 15:17`
- `C-M-q` = `indent-sexp` (Like vim `=`) # `mplayer slime.mov -ss 17:05`
- `C-c C-k` compiles whole file # `mplayer slime.mov -ss 19:15`
- SLIME's completion. Is fuzzy with visible # `mplayer slime.mov -ss 19:39`
- SLIME debugger. # `mplayer slime.mov -ss 22:22`
  * Error message at top
  * "Restarts"
    - `0` aka `a` = Abort-restart (maybe like pry's `restart` ?)
    - `1` Exit debugger (maybe like pry's `^D`)
    - it doesn't seem like there's something as cool as pry-rescue's `try-again`
  * Backtrace
    - Starts abbreviated, but has a `--more--` link.
    - `t` = toggle details (shows locals, like pry-de's `,loc`)
    - `v` = show-source (like pry's `edit-method`); creates an unfocused window with the buffer for the file with that code.
- `(declaim (optimize …))` - Adjusting compilation details for debugging. # `mplayer slime.mov -ss 25:42`
- Emacs tools for writing Lisp # `mplayer slime.mov -ss 27:05`
    - Opens with automatic close (like vim's `delimitMate`)
    - A few text editing tricks, either not needed in Ruby or easily done with `ruby.vim` features.
- Result of compilation with faults # `mplayer slime.mov -ss 33:35`
    - Editor gets underlines with errors ← red, warnings ← orange, style warnings ← yellow.
    - Window below with result (is hyperlinked so following fault jumps to spot in editor buffer)
    - Strategies for API discovery: # `mplayer slime.mov -ss 35:49`
        * Hyperspec docs (`C-c C-d h`, above)
        * `M-.` = find definition, AKA "Meta-Point" (like pry's `edit-method`
          or `$`)
        * describe-symbol to look for macro docs
        * `C-c RET` = `slime-macroexpand-1` (would be cool if we cold hack this in in Ruby. Not going to be as clean, but it could be possible to see increasing recursions of show-source, perhaps)
- `(require :asdf-install)` (like `gem install`) # `mplayer slime.mov -ss 37:40`
    - Does the cool installtion idiom where you get a prompt on a split `$PATH`, asking which of the pieces to use as the dest dir.
    - A GPG signature error occurs, and somehow the SLIME debugger knows to offer a `skip-gpg-check` Restart in addition to the "Abort" and "Exit" of before. Maybe pry should have an error handling framework that acts similarly.
- Package description via SLIME inspector (similar to inspecting a # `mplayer slime.mov -ss 39:00`
  Gem::Specification - note that this is an example of the kind of thing where
  Ruby code works almost as well as Pry commands. It isn't as polished,
  definitely not as obvious to people who don't know about the APIs, but also
  easy to implement as commands if we want, e.g. `gem-spec treetop ⇒
  output.puts Gem::Specification.find_by_name('treetop').to_yaml`)
- `M-.` (like `show-source`) works inside a line. This is trickier with the namespacing of Ruby. Poor man's fix is to just show a menu of all classes that implement the given message, and let the user show-source on the one that makes most sense. # `mplayer slime.mov -ss 45:17`
- `C-c C-t` - enables tracing on function, from editor buffer. (We need "`pry-trace`" first) # `mplayer slime.mov -ss 45:24`
- Recursive inspection (showing guts of a lambda. There is a way to do similar in pry, I think requiring `ruby4ruby`, so you can inspect blocks/`Proc`s/etc.) # `mplayer slime.mov -ss 47:32`
- `i` - `sldb-inspect-in-frame`, evaluate in context of frame (like the way `pry-stack\_explorer` has always acted, letting you go `up` then run REPL code with the locals visibile like `whereami` shows) # `mplayer slime.mov -ss 48:18`
- `C-c <` - `slime-list-callers` - Reverse tags, like `[cscope](http://cscope.sourceforge.net/)` or maybe `[ncc](http://students.ceid.upatras.gr/~sxanth/ncc/)`. Really good to see this in a dynamic language — must figure out how they do it. I still picture tracing a live execution then using that list, but maybe there's a way to figure it out. Must test this `slime-list-callers` on some dynamically generated symbols to see if it handles these cases. # `mplayer slime.mov -ss 50:41`
  * Both of these are hyperlinked to edit the files that own them. (I'm thinking of some list that commands can manage to be args for `edit`. So you could list through a query like this, then `edit -f3` to get the 3rd on the list)
- `C-c >` - `slime-list-callees` - List every function a function calls. # `mplayer slime.mov -ss 51:54`

- pry-de.vim could liberate vim from dead-coding tyranny, giving access to the live data:
  * the ruby process behind Pry should run detached from the REPL (like `ruby-dev.el`, probably using `pry-remote-em`)
  * then we do something like:
    Pry.config.editor += ' +let\\ g:pry_de_proc='+$? if Pry.config.editor[/^vim/]

<a name="open-genera"/>
An old Lisp machine, [Open Genera](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=30m)
  - The REPL responds to completed expressions instantly with an evaluation [@34m](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=34m)
  - When prompted for a string type, you can click on the previously-displayed
    strings (and it's smart enough not to let you click on numbers, because
    the context is wrong) [@35m](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=35m)
    - Keyboard lover's note: The mouse isn't necessary for this, you could just
      narrow the pool for tabcompletion.
  - The data display is nested (and zoomable) [@35m30s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=35m30s)
  - The objects update even if you go back in the history and change the
    values [@36m](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=36m)
  - Fuzzy-finding [@37m](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=37m)
  - Keyword context (like args completion) [@37m30s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=37m30s)
  - Mentions [CLIM](https://en.wikipedia.org/wiki/CLIM), a successor of the
    Symbolics machines.
  - Asynchronous output to terminal (and mostly nondisruptive, and optional
    even when they would be trouble)
  - Clickable pointers (zooming in) [@42m30s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=42m30s)
  - Interactive documentation, with a smartish tree context [@43m00s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=43m00s)
  - Runnable examples in docs [@45m](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=45m)
  - Creating a record, getting an error — beginning of debug sequence [@45m35s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=45m35s)
    - Ctrl+b = backtrace, clickable.
    - Cool little scrollbar UI dotted line
  - "More interesting" backtrace [@47m45s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=47m45s)
    - Ctrl+e to hop to editor
    - Stack-down [@49m30s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=49m30s)
    - Pull up temp REPL while editing [@51m30s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=51m30s)
    - Holding down Ctrl makes the UI work charwise, instead of object-wise (like holding down Shift in a mouse-aware ncurses program)
    - Forgetting a paren, just to remind us he's using Lisp (though, he says the tools would have warned him if he had've taken a few more steps).
    - M-x start-patch (like commit+push with git) [@55m](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=55m)
    - M-x start-private-patch (like local commit with git)
    - Using basic object display as GUI tool for commit task [@56m45s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=56m45s)
  - A mess+solution regarding Common Lisp namespacing.
  - Version control at the filesystem level.
  - Graphics output to console [@1h2m30s](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=1h2m30s)
  - Peeking into running process [@1h07m](https://docs.google.com/a/foo.com/file/d/0Bw4Wz8Ir0pl1cmNRaHYwdU1wdXM/preview?pli=1#t=1h07m)

Multiple views of a project (not restricted to a single, serialized
organization):
- http://multiview.cs.pdx.edu/
- Charles Simonyi's work: http://www.edge.org/digerati/simonyi/simonyi_p2.html ; ftp://ftp.research.microsoft.com/pub/tr/tr-95-52.doc 

- [Agda + Emacs](http://code.haskell.org/Agda/src/data/emacs-mode/agda2-mode.el) seems interesting
  - One cool thing is how you can leave parts unimplemented, then the system
    helps you fill those in. Perhaps this would apply to Ruby in the form of
    TODO-tests and [Yada Yada](https://github.com/ConradIrwin/yada_yada)s?

- [Blocky](http://blocky.io/)

- https://github.com/dmbarbour/Sirea

- [Field](http://openendedgroup.com/field/OverviewBanners2.html)

Hopping around has to be as convenient (or more) than using:
- vim
    - with https://github.com/kien/ctrlp.vim
    - with https://github.com/tpope/vim-rails
- zsh with https://github.com/zsh-users/zaw

We should be able to do everything we that we can currently do from
vim/emacs/TextMate/etc., faster and with less distraction. (That "distraction"
piece is why users click through inefficient menus with their mouse rather
than learn the shortcuts — because the time it takes to stop and learn the
efficient ways seems like too big a detraction from the current task. An
example of what I mean by "with less distraction" is how you can exploratively
Tab-complete things in Pry. You may not remember the method you're looking
for, but after a few tabs you'll see it and remember without forgetting why
you were headed there in the first place.)


The Future
----------

Some things that might happen:

- It could become standard practice to make processes have a pry-remote
  backdoor for debugging/runtime-feature-addition.
- `pry-exception_explorer` could be ready at all times, with an interface
  similar to the menu provided by the `hammertime` gem.
- Could drive more advanced terminal interaction, such as
  [Termkit](http://acko.net/blog/on-termkit/) (or perhaps even use the gruesome
  w3m+xterm hack to display graphics.)
- ["automatic debugging"](http://www2.cs.uidaho.edu/~jeffery/aadebug.html)
- epitron ideas:
    - [Viewpoints Research Institute](http://vpri.org/html/writings.php)
        - Especially STEPS, by Alan Kay. Working bits include:
          - http://tinlizzie.org/ometa/
          - http://piumarta.com/software/peg/
          - http://piumarta.com/software/maru/
    - http://chris.ill-logic.com/wiki/HCI/Speech_vs._Vision
    - Brett Victor's [recent talk about live programming](http://vimeo.com/36579366) (http://worrydream.com) Learnable Programming http://goo.gl/fvRxY  Explorable Explanations http://goo.gl/8K99j  Inventing on Principle http://goo.gl/5rxDx
    - [Aza Raskin (Mozilla
      Labs)](http://www.alistapart.com/articles/neveruseawarning)
    - http://chris.ill-logic.com/wiki/HCI/Speech_vs._Vision
    - http://www.cs.iastate.edu/~design/vmil/2011/papers/p02-kell.pdf
    - http://arxiv.org/abs/q-alg/9705009
    - Jaron Lanier
    - http://subtextual.org/ especially http://subtextual.org/demo1.html
    - live/metacoding - "a company built by charles simonyi, ex senior
      software architect at microsoft, working on making that easy intentional
      software (intentsoft.com)"
    - [Content Centric
      Networking](http://en.wikipedia.org/wiki/Content-centric_networking) |
      http://www.youtube.com/watch?v=gqGEMQveoqg
