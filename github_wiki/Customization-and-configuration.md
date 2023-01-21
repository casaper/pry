# Customizing Pry

<a name="Back_to_top">

### Quick Menu:

* <a href="#Config_per_instance">Per-instance customization</a>
* <a href="#Config_color">Color</a>
* <a href="#Config_pager">Pager</a>
* <a href="#Config_auto_indent">Auto indent</a>
* <a href="#Config_correct_indent">Correct indent</a>
* <a href="#Config_prefix">The command prefix</a>
* <a href="#Config_history">History</a>
* <a href="#Config_editor">Editor</a>
* <a href="#Config_plugin_loading">Plugin loading</a>
* <a href="#Config_rc_loading">RC-file loading</a>
* <a href="#Config_inp_out_cache_size"> The \_in\_ and \_out\_ cache size</a>
* <a href="#Config_prompt">The prompt</a>
* <a href="#Config_prompt_name">The prompt prefix</a>
* <a href="#Config_input">The input object</a>
* <a href="#Config_output">The output object</a>
* <a href="#Config_print">The print object</a>
* <a href="#Config_exception">The exception handler</a>
* <a href="#Config_exception_whitelist">The exception whitelist</a>
* <a href="#Config_exception_window_size">The exception window size</a>
* <a href="#Config_commands">The command set object</a>
* <a href="#Config_runtime">Runtime customization</a>
* <a href="#Config_index_of_config_options">Index of Config Options</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

Pry allows a fair degree of customization via an easy to use
API. Such customizations may be inserted in a user's
[`.pryrc`](https://github.com/pry/pry/wiki/Pry-rc) file, or even
[distributed as a plugin](https://github.com/pry/pry/wiki/Plugins). We list below a number of config options and demonstrate how they can be used to
customize Pry.

Note that config options set through `Pry.config` only define a default value which
specific Pry instances can override. See <a href="#Config_per_instance">Per-instance customization</a> for more information.

<a name="Config_per_instance">

### Per-instance customization

`Pry.config` provides a default for all Pry instances. It is typically used in a
`.pryrc` file to change the default behaviour of Pry. After a Pry repl session has
started it will inherit its defaults from `Pry.config` but use its own local
configuration object that you can access through `pry_instance.config` in a repl session.

To change configuration settings in a repl session you should use `pry_instance.config`
instead of `Pry.config` or your changes might not be seen by Pry commands as
they read values from `pry_instance.config` instead of going directly through `Pry.config`.

<a href="#Back_to_top">Back to top</a>

<a name="Config_color">

### Color

`Pry.config.color` is a boolean option determining whether color
will be used. Setting it to `false` disables color (i.e syntax
highlighting and other color effects).

Note that the `toggle-color` command can also be used to toggle color
on and off while in a session.

**Default value:** `true`

Example: Disabling color

Permanently (in a .pryrc file)
```ruby
Pry.config.color = false
```

Temporarily (in a repl session)
```ruby
pry_instance.config.color = false
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_pager">

### Pager

`Pry.config.pager` is a boolean option determining whether a pager
will be used for long output. Setting it to `false` disables the
pager.

Currently only the `less` pager is supported, though a very simple
custom pager is used if `less` is not available.

It may be necessary to turn paging off if you are running Pry from within
an emacs shell or similar.

**Default value:** `true`

Example: Disabling paging

Permanently (in a .pryrc file)
```ruby
Pry.config.pager = false
```
Temporarily (in a repl session)
```ruby
pry_instance.config.pager = false
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_auto_indent">

### Auto indent

`Pry.config.auto_indent` is a boolean option determining whether automatic indenting of input will occur. Setting it to `false` disables automatic indenting.

**Default value:** `true`

Example: Disabling auto indenting

Permanently (in a .pryrc file)
```ruby
Pry.config.auto_indent = false
```
Temporarily (in a repl session)
```ruby
pry_instance.config.auto_indent = false
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_correct_indent">

### Correct indent

`Pry.config.correct_indent` is a boolean option determining whether correction of indenting will occur (requires [auto\_indent](https://github.com/pry/pry/wiki/_preview#wiki-Config_auto_indent) to be set to `true`). Setting it to `false` disables correction.

Correction of indenting typically happens when a final `end` is entered for a block of code. Pry will determine the proper indentation level for the `end` and will adjust its indentation accordingly.

Indentation correction makes use of [ansi codes](http://en.wikipedia.org/wiki/ANSI_escape_code) that most terminals should support, but some may not. If you find that indentation correction is causing strange behavior on your terminal, you should consider turning this option off.

**Default value:** `true`

Example: Disabling correcting of indentation

Permanently (in a .pryrc file)
```ruby
Pry.config.correct_indent = false
```
Temporarily (in a repl session)
```ruby
pry_instance.config.correct_indent = false
```
<a href="#Back_to_top">Back to top</a>

<a name="Config_prefix">

### The command prefix

`Pry.config.command_prefix` is an option determining which command
prefix (if any) should be used.

More detailed information on the command prefix can be found in the
[Command system section](https://github.com/pry/pry/wiki/Command-system#wiki-Command_prefix)

**Default value:** `""` (no prefix)

Example: Change the command prefix to `%`

Permanently (in a .pryrc file)
```ruby
Pry.config.command_prefix = "%"
```
Temporarily (in a repl session)
```ruby
pry_instance.config.command_prefix = "%"
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_history">

### History

`Pry.config.history` is an option determining various properties of
Pry history. It has three sub-properties:

* `Pry.config.history_save` is a boolean option, it defaults to `true`
* `Pry.config.history_load` is a boolean option, it defaults to `true`
* `Pry.config.history_file` is a string option, it defaults to `~/.pry_history`

More detailed information on Pry history can be found in the [history section](/pry/pry/wiki/History)

Example: Change the history file to `~/.irb_history`

Permanently (in a .pryrc file)
```ruby
Pry.config.history_file = "~/.irb_history"
```
Temporarily (in a repl session)
```ruby
pry_instance.config.history_file = "~/.irb_history"
```

Example: Put the history file in the current directory, for a separate history per-project:

Permanently (in a .pryrc file)
```ruby
Pry.config.history_file = ".pry_history"
```
Temporarily (in a repl session)
```ruby
pry_instance.config.history_file = ".pry_history"
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_editor">

### Editor

`Pry.config.editor` is an option determining which editor will be used
by default by Pry commands.

It accepts either a string or a callable (i.e a `proc`). More detailed
information on this config option can be found in the  [Editor
Integration](https://github.com/pry/pry/wiki/Editor-integration) section.

**Default value:** `$EDITOR` (or `nano` if `$EDITOR` not defined)

Example: Setting a String

Permanently (in a .pryrc file)
```ruby
Pry.config.editor = "emacsclient"
```
Temporarily (in a repl session)
```ruby
pry_instance.config.editor = "emacsclient"
```

Example: Setting a proc

Permanently (in a .pryrc file)
```ruby
Pry.config.editor = proc { |file, line| "emacsclient +#{line} #{file}" }
```
Temporarily (in a repl session)
```ruby
pry_instance.config.editor = proc { |file, line| "emacsclient +#{line} #{file}" }
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_plugin_loading">

### Plugin loading

`Pry.config.should_load_plugins` is a boolean option determining
whether plugins should be loaded. Setting it to `false` disables
plugin loading.

It may be useful to set this option in your [`.pryrc`
file](https://github.com/pry/pry/wiki/Pry-rc) or in your program when [invoking Pry at
runtime](https://github.com/pry/pry/wiki/Runtime-invocation)

**Default value:** `true`

Example: Disabling plugin loading

```ruby
Pry.config.should_load_plugins = false
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_rc_loading">

### RC-file loading

`Pry.config.should_load_rc` is a boolean option determining
whether the rc file (`.pryrc`) should be loaded. Setting it to `false` disables
RC file loading.

It only makes sense to set this option when [invoking Pry at
runtime](https://github.com/pry/pry/wiki/Runtime-invocation), however
RC file loading can be disabled at the command line by running the Pry
executable with the `-f` option.

**Default value:** `true`

Example: Disabling RC-file loading

```ruby
Pry.config.should_load_rc = false
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_inp_out_cache_size">

### The \_in\_ and \_out\_ cache size

`Pry.config.memory_size` is an option determining the size of the
`_in_` and `_out_` cache. More detailed information on this option is
found in the [Entering Input](https://github.com/pry/pry/wiki/User-Input) section.

**Default value:** 100

Example: Increasing the size of the cache

Permanently (in a .pryrc file)
```ruby
Pry.config.memory_size = 300
```
Temporarily (in a repl session)
```ruby
pry_instance.config.memory_size = 300
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_prompt">

### The prompt

**Note: The change-prompt command can be used as an alternative to directly
changing the configuration object while in a repl session**

`Pry.config.prompt` is an option determining the prompt displayed to
the user when awaiting input.

Pry can accept two prompt-types for every prompt - the 'main prompt' and the 'wait prompt'. The main prompt is always used for the first line of input; the wait prompt is used in multi-line input to indicate that the current expression is incomplete and more lines of input are required.

A valid Pry prompt is a `Pry::Prompt` object. This object should be instantiated with a name, description, and an array of either a single Proc object or two Proc objects. When two Procs are provided the first is the 'main prompt' and the last is the 'wait prompt'. When a single Proc object is used it will be used for both the main prompt and the wait prompt.

Three parameters are passed into the prompt procs and can be used in the prompt if desired:
1. the object that is the target of the session
2. the current nesting level
3. a reference to the associated Pry instance

**Default value:** `Pry::Prompt[:default]`

Example: Using one proc for both main and wait prompts

Permanently (in a .pryrc file)
```ruby
Pry.config.prompt = Pry::Prompt.new(
  "custom",
  "my custom prompt",
  [proc { |obj, nest_level, _| "#{obj}:#{nest_level}> " }]
)
```
Temporarily (in a repl session)
```ruby
pry_instance.prompt = Pry::Prompt.new(
  "custom",
  "my custom prompt",
  [proc { |obj, nest_level, _| "#{obj}:#{nest_level}> " }]
)
```

Example: Alternatively, provide two procs; one for main and one for wait

Permanently (in a .pryrc file)
```ruby
Pry.config.prompt = Pry::Prompt.new(
  "custom",
  "my custom prompt",
  [ proc { "ENTER INPUT> " }, proc { "MORE INPUT REQUIRED!*" }]
)
```
Temporarily (in a repl session)
```ruby
pry_instance.prompt = Pry::Prompt.new(
  "custom",
  "my custom prompt",
  [ proc { "ENTER INPUT> " }, proc { "MORE INPUT REQUIRED!*" }]
)
```

Example: Overriding the default at instantiation

```ruby
Pry.start(
  binding,
  :prompt => Pry::Prompt.new(
    "custom",
    "my custom prompt",
    [ proc { "ENTER INPUT> " }, proc { "MORE INPUT REQUIRED!*" }]
  )
)
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_prompt_name">

### The prompt name

`Pry.config.prompt_name` is an option determining the string that prefixes the pry prompt.  It accepts any string value. You would typically customize this on a per project basis so that the pry prompt reflects the name of the project you are in.

**Default value:** pry

Example: Setting the prompt name to match the folder name of your current Rails project.  Put this in `#{ Rails.root }/.pryrc`

Permanently (in a .pryrc file)
```ruby
Pry.config.prompt_name = File.basename(Dir.pwd)
```
Temporarily (in a repl session)
```ruby
pry_instance.config.prompt_name = File.basename(Dir.pwd)
```

Example: Setting the prompt name to whatever you want.

Permanently (in a .pryrc file)
```ruby
Pry.config.prompt_name = 'my_project_name'
```
Temporarily (in a repl session)
```ruby
pry_instance.config.prompt_name = 'my_project_name'
```

<a name="Config_input">

### The input object

`Pry.config.input` is an option determining the input object - the
object from which Pry retrieves its lines of input. Pry accepts any object that
implements the `readline` method. This includes `IO` objects, `StringIO`,
`Readline`, `File` and custom objects. When creating a custom object, you may
also want to implement the `completion_proc=` and `completion_proc` methods.

Note it is important to ensure that the last line of input is `exit`
if you are running non-interactively as the input object will be reset
to `Pry.config.input` on `EOF` and potentially loop forever waiting
for input.

**Default value:** `Readline`

Example: Setting the input object to a `StringIO`

```ruby
Pry.config.input = StringIO.new("@x = 10\nexit")
class Dog
end
milo = Dog.new
milo.pry

milo.instance_variable_get(:@x) #=> 10
```

Example: Overriding the default at instantiation

```ruby
Pry.start binding, :input => StringIO.new("ls\nexit")
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_output">

### The output object

`Pry.config.output` is an option determining the output object - the
object to  which Pry writes its output. Pry accepts any object that implements the `puts` method. This includes `IO` objects, `StringIO`, `File` and custom objects.

**Default value:** `$stdout`

Example: Setting the output object to a `StringIO`

```ruby
Pry.config.output = StringIO.new
```

Example: Overriding the default at instantiation

```ruby
Pry.start binding, :output => StringIO.new
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_print">

### The print object

`Pry.config.print` is an option determining the print object - the
`Proc` responsible for displaying expression evaluation output.

Two  parameters are passed to the print Proc: these  are the output
object for the current session and the expression value to print. It
is important that you write to the `output` object instead of just
stdout so that all Pry output can be redirected if necessary.

**Default value:** `Pry::DEFAULT_PRINT`

Example: IRB-style evaluation output

Permanently (in a .pryrc file)
```ruby
Pry.config.print = proc { |output, value| output.puts "=> #{value.inspect}" }
```

Temporarily (in a repl session)
```ruby
pry_instance.config.print = proc { |output,value| output.puts "=> #{value.inspect}" }
```

Example: Overriding the default at instantiation

```ruby
Pry.start binding, :print => proc { |output, value| output.puts "=> #{value.inspect}" }
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_exception">

### The exception handler

`Pry.config.exception_handler` is an option determining the exception handler object - the `Proc` responsible for dealing with exceptions raised by user input to the REPL.

Three parameters are passed to the exception handler Proc: these are the output
object for the current session, the exception object that was
raised inside the Pry session, and a reference to the associated Pry instance. As mentioned in the <a href="#Config_print">print object</a>
section (above) it is important you write to the `output` object and
not just stdout.

**Default value:** `Pry::DEFAULT_EXCEPTION_HANDLER`
```ruby
  Pry.config.exception_handler = proc do |output, exception, _|
    output.puts "#{exception.class}: #{exception.message}"
    output.puts "from #{exception.backtrace.first}"
  end
```

**Documentation:** [Customising the exception handler](Exceptions#wiki-Exception_handler)

<a href="#Back_to_top">Back to top</a>

<a name="Config_exception_whitelist">

### The exception whitelist

`Pry.config.exception_whitelist` is an option determining which exceptions Pry should not catch.

**Default value:** `[SystemExit, SignalException]`

**Documentation:** [Configuring which exceptions are caught](Exceptions#wiki-Exception_whitelist)

<a href="#Back_to_top">Back to top</a>

<a name="Config_exception_window_size">

### The exception window size

`Pry.config.default_window_size` tells Pry how many lines `cat --ex` should show before and after the line that raised an Exception.

**Default value:** `5`

**Documentation:** [Configuring `cat --ex`](Exceptions#wiki-Exception_window_size)

<a href="#Back_to_top">Back to top</a>

<a name="Config_commands">

### The command set object

`Pry.config.commands` is an option determining the commands object - the
command set (`Pry::CommandSet`) responsible for providing the commands
for the session.

**Default value:** `Pry::Commands`

More detailed information on Pry commands can be found in the [commands section](https://github.com/pry/pry/wiki/Command-system)

Example:

```ruby
Pry.config.commands = Pry::CommandSet.new do
    import_from Pry::Commands, "ls"
    command "greet" do |name|
        output.puts "hello #{name}"
    end
end
```

Example: Overriding the default at instantiation

```ruby
Pry.start binding, :commands => Pry::ExtendedCommands::Experimental
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_runtime">

### Runtime customization

Inside a Pry session there is a [special \_pry\_ local variable](https://github.com/pry/pry/wiki/Special-Locals#wiki-Pry_local) which
represents the Pry instance managing the current session. We can
simply assign to the relevant accessor on the `pry_instance.config` object to
change the option we want.

Example: Change the prompt at runtime

```ruby
pry_instance.prompt = proc { "> " }
```

Example: Change the input object at runtime

```ruby
pry_instance.input = StringIO.new("ls")
```

<a href="#Back_to_top">Back to top</a>

<a name="Config_index_of_config_options">

### Index of Config Options

Feature                  | `Pry.config` Accessor     | Description
-----------------------  | ------------------------- | -----------
Auto Indent              | `auto_indent`             | Boolean determining whether automatic indenting of input will occur. Defaults to `true`.
Collision Warning        | `collision_warning`       | Boolean determining whether a warning is shown if a command collides with a local/method in the current scope. Defaults to `false`.
Color                    | `color`                   | Boolean determining whether color will be used. Defaults to `true`.
Command Completions      | `command_completions`     | Object used to generate possible command completions. Defaults to `proc { commands.keys }`.
Command Prefix           | `command_prefix`          | When present, commands will not be acknowledged unless they are prefixed with the given string. Defaults to `""`.
CommandSet Object        | `commands`                | The `Pry::CommandSet` responsible for providing commands to the session. Defaults to `Pry::Commands`.
Completer Object         | `completer`               | The object class that is used to generate possible completions. Defaults to `Pry::InputCompleter`.
Control-d handler        | `control_d_handler`       | Proc used to handle when `CTRL-d` is pressed. Defaults to `Pry::DEFAULT_CONTROL_D_HANDLER`.
Disable Auto Reload      | `disable_auto_reload`     | Boolean for turning off auto reloading performed by `edit-method` and related commands. Defaults to `false`.
Editor                   | `editor`                  | String or Proc determining what editor should be used. Defaults to `ENV["EDITOR"]`.
Exception Handler        | `exception_handler`       | Proc responsible for handling exceptions raised by user input to the REPL. Defaults to `Pry::DEFAULT_EXCEPTION_HANDLER`.
Exception White-list     | `exception_whitelist`     | A list of exceptions that Pry should not catch. Defaults to `[SystemExit, SignalException]`.
Exception Window Size    | `default_window_size`     | How many lines of context should be shown around the line that raised an exception. Defaults to `5`.
Exec String              | `exec_string`             | A line of code to execute in context before the session. Defaults to `""`.
Extra Sticky Locals      | `extra_sticky_locals`     | Hash of objects that persist between all bindings in a session. Defaults to `{}`.
File Completions         | `file_completions`        | Object used to generate possible file name completions. Defaults to `proc { Dir["."] }`.
Gist Command Config      | `gist`                    | Config for the `gist` command. Defaults to `{ :inspecter => proc(&:pretty_inspect) }`.
History                  | `history`                 | Configuration object of [history-related configurations](/pry/pry/wiki/History).
Hooks Object             | `hooks`                   | Object tracking the callbacks registered for each hook event. Defaults to `Pry::DEFAULT_HOOKS`.
Indent Correction        | `correct_indent`          | Boolean determining whether correcting of indenting will occur. Defaults to `true`.
Input Object             | `input`                   | The object from which Pry retrieves lines of input. Defaults to `Readline`.
Input Stack              | `input_stack`             | The object from which Pry retrieves lines of input. Defaults to `Readline`.
Local RC loading         | `should_load_local_rc`    | Boolean determining whether to load any `.pryrc` file that may exist in the current directory (`./.pryrc`). Defaults to `true`.
Ls Command Config        | `ls`                      | Config for the `ls` command. Defaults to `Pry::Command::Ls::DEFAULT_OPTIONS`.
Memory Size              | `memory_size`             | Determines the size of the `_in_` and `_out_` cache. Defaults to `100`.
Output Object            | `output`                  | The object to which Pry writes its output. Defaults to `$stdout`.
Pager                    | `pager`                   | Boolean determining whether a pager will be used for long output. Defaults to `true`.
Plugin Loading           | `should_load_plugins`     | Boolean determining whether plugins should be loaded. Defaults to `true`.
Print Object             | `print`                   | The object responsible for displaying expression evaluation output. Defaults to `Pry::DEFAULT_PRINT`.
Prompt                   | `prompt`                  | A Proc or an Array of two Procs that will be used to determine the prompt. [Read more][Pry Wiki - Customization and configuration - The Prompt].
Prompt Name              | `prompt_name`             | String that prefixes the prompt. Defaults to `pry`.
Prompt Safe Objects      | `prompt_safe_objects`     | Collection of objects that are safe to display with `#inspect`. Defaults to `[String, Numeric, Symbol, nil, true, false]`.
Pry Doc Presence         | `has_pry_doc`             | Boolean indicating whether `pry-doc` plugin has been loaded. Defaults to `nil`.
RC-file Loading          | `should_load_rc`          | Boolean determining whether `.pryrc` files should be load. Defaults to `true`.
Required Libraries       | `requires`                | Collection of libraries that should be required by Pry. Defaults to `[]`.
Required Library Loading | `should_load_requires`    | Boolean determining whether required libraries should be loaded. Defaults to `true`.
System Command           | `system`                  | Proc that defines how Pry should execute system commands. Defaults to `Pry::DEFAULT_SYSTEM`.
Trap Interrupts          | `should_trap_interrupts`  | Boolean determining whether Pry should take extra effort to trap interrupts. Defaults to `true` on JRuby and `false` on other platforms.
Windows Console Warning  | `windows_console_warning` | Boolean determining whether Windows users should be warned to use `ansicon`. Defaults to `true`.

<a href="#Back_to_top">Back to top</a>
