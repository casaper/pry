# Live help system

<a name="Back_to_top">

### Quick Menu:

* <a href="#View_pry_docs">Viewing a Pry method's documentation</a>
* <a href="#Pry_config_docs">Learning about Pry's configuration options</a>
* <a href="#Pry_command_docs">The `help` command (Pry command documentation)</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

One of the design goals of Pry was for it to be
self-documenting. To that end, Pry provides commands that yield
documentation on nearly every aspect of Pry from within the REPL itself.

<a name="View_pry_docs">

### Viewing a Pry method's documentation

Since Pry is simply Ruby code, all documentation on its methods are
available using the standard
[`show-doc`](https://github.com/pry/pry/wiki/Documentation-browsing)
command.

Example:

```
pry(main)> ? Pry#valid_expression?

From: /Users/john/.rvm/gems/ruby-1.9.2-p180/gems/pry-0.9.2/lib/pry/pry_instance.rb @ line 458:
Number of lines: 7

signature: valid_expression?(code)

Determine if a string of code is a valid Ruby expression.
Ruby 1.9 uses Ripper, Ruby 1.8 uses RubyParser.
param [String] code The code to validate.
return [Boolean] Whether or not the code is a valid Ruby expression.
example
  valid_expression?("class Hello") #=> false
  valid_expression?("class Hello; end") #=> true
pry(main)>
```

<a href="#Back_to_top">Back to top</a>

<a name="Pry_config_docs">

### Learning about Pry's configuration options

Live help may be particularly useful when learning about Pry's
configuration options. Most of these are documented methods in the
`Pry.config` namespace.

Example:

```
pry(main)> ls -m Pry.config
[:__binding_impl__, :color, :color=, :commands, :commands=, :editor, :editor=, :exception_handler, :exception_handler=, :has_pry_doc, :has_pry_doc=, :history, :history=, :hooks, :hooks=, :input, :input=, :memory_size, :memory_size=, :output, :output=, :pager, :pager=, :plugins, :plugins=, :print, :print=, :prompt, :prompt=, :result_pager, :result_pager=, :results_pager, :results_pager=, :should_load_plugins, :should_load_plugins=, :should_load_rc, :should_load_rc=]
pry(main)> ? Pry.config.editor

From: /Users/john/.rvm/gems/ruby-1.9.2-p180/gems/pry-0.9.2/lib/pry/config.rb @ line 58:
Number of lines: 12

signature: editor()

The default editor to use. Defaults to $EDITOR or nano if
$EDITOR is not defined.
If editor is a String then that string is used as the shell
command to invoke the editor. If editor is callable (e.g a
Proc) then file and line are passed in as parameters and the
return value of that callable invocation is used as the exact
shell command to invoke the editor.
example String
  Pry.editor = "emacsclient"
example Callable
  Pry.editor = proc { |file, line| "emacsclient #{file} +#{line}" }
return [String, #call]
pry(main)>
```

<a href="#Back_to_top">Back to top</a>

<a name="Pry_command_docs">

### The `help` command (Pry command documentation)

Pry's commands are not methods and so cannot easily be
documented in the same way as methods (using `show-doc`). Instead commands have their own
documentation system organized around the `help` command.

Typing `help` at the prompt will retrieve a list of all available
commands. If you pass a parameter to `help` where that parameter is
the name of a command you will restrict the output to the help for just that command.

Example: Invoking `help` without arguments

```
Help
  help               Show a list of commands or information about a specific command

Context
  cd                 Move into a new context (object or scope).
  find-method        Recursively search for a method within a Class/Module or the current namespace.
  ls                 Show the list of vars and methods in the current scope.
  pry-backtrace      Show the backtrace for the Pry session.
  raise-up           Raise an exception out of the current pry instance.
  reset              Reset the REPL to a clean state.
  whereami           Show code surrounding the current context.
  wtf?               Show the backtrace of the most recent exception.

Editing
  !                  Clear the input buffer.
  amend-line         Amend a line of input in multi-line mode.
  edit               Invoke the default editor on a file.
  hist               Show and replay Readline history.
  play               Playback a string variable or a method or a file as input.
  show-input         Show the contents of the input buffer for the current multi-line expression.

Introspection
  ri                 View ri documentation.
  show-doc           Show the documentation for a method or class.
  show-source        Show the source for a method or class.
  stat               View method information and set _file_ and _dir_ locals.

Gems
  gem-cd             Change working directory to specified gem's directory.
  gem-install        Install a gem and refresh the gem cache.
  gem-list           List and search installed gems.
  gem-open           Opens the working directory of the gem in your editor

Commands
  import-set         Import a Pry command set.
  install-command    Install a disabled command.

Aliases
  !!!                Alias for `exit-program`
  !!@                Alias for `exit-all`
  $                  Alias for `show-source`
  ?                  Alias for `show-doc`
  @                  Alias for `whereami`
  clipit             Alias for `gist --clip`
  file-mode          Alias for `shell-mode`
  history            Alias for `hist`
  jist               Alias for `gist`
  quit               Alias for `exit`
  quit-program       Alias for `exit-program`
  reload-method      Alias for `reload-code`
  show-method        Alias for `show-source`

Input and Output
  .<shell command>   All text following a '.' is forwarded to the shell.
  cat                Show code from a file, Pry's input buffer, or the last exception.
  fix-indent         Correct the indentation for contents of the input buffer
  save-file          Export to a file using content from the REPL.
  shell-mode         Toggle shell mode. Bring in pwd prompt and file completion.

Misc
  gist               Playback a string variable or a method or a file as input.
  pry-version        Show Pry version.
  reload-code        Reload the source file that contains the specified code object.
                     For example, 'reload-code MyClass' reloads the file that defines
                     the MyClass class.
  simple-prompt      Toggle the simple prompt.
  toggle-color       Toggle syntax highlighting.

Navigating Pry
  !pry               Start a Pry session on current self.
  disable-pry        Stops all future calls to pry and exits the current session.
  exit               Pop the previous binding.
  exit-all           End the current Pry session.
  exit-program       End the current program.
  jump-to            Jump to a binding further up the stack.
  nesting            Show nesting information.
  switch-to          Start a new subsession on a binding in the current stack.
```

Note that `help` only returns a short description of each command,
but this is sufficient in many cases. For more complex commands the
description (retrieved by `help`) will indicate that more
documentation is available by passing the `--help` switch to the
command itself.

Example: A simple command

```
pry(main)> help cd
Start a Pry session on VAR (use `cd ..` to go back and `cd /` to return to Pry top-level)
pry(main)>
```

Example: A command with further documentation available

```
pry(main)> help show-method
Show the source for METH. Type `show-method --help` for more info. Aliases: $, show-source
pry(main)> show-method --help
Usage: show-method [OPTIONS] [METH]
Show the source for method METH. Tries instance methods first and then methods by default.
e.g: show-method hello_method

options:

    -l, --line-numbers          Show line numbers.
    -b, --base-one              Show line numbers but start numbering at 1 (useful for `amend-line` and `play` commands).
    -M, --instance-methods      Operate on instance methods.
    -m, --methods               Operate on methods.
    -f, --flood                 Do not use a pager to view text longer than one screen.
    -c, --context               Select object context to run under.
    -h, --help                  This message.
pry(main)>
```

<a href="#Back_to_top">Back to top</a>



