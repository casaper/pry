
### Overview

Pry ships with a large number of builtin commands to cover common use-cases. Unfortunately we can't hope to include every command that might be useful to anyone, so we also make it easy to define new commands for yourself. This page explains how to do so.

For further explanation of the concepts behind commands, and how to use them, see the page on the [[command system]].

### Where to put your commands

You can type your commands directly in to the Pry interpreter, but if you want to persist them globally, put them in your [.pryrc](https://github.com/pry/pry/wiki/Pry-rc) file.

### Block commands

Block commands are a quick way of definining simple commands, unlike <a href="#Class_commands">Class commands</a> they don't do any options parsing making them only suitable for quick hacks and personal utilities.

To create a new block command, you call `Pry::CommandSet#block_command`, for example:

```ruby
Pry::Commands.block_command "hello", "Say hello to three people" do |x, y, z|
  output.puts "hello there #{x}, #{y}, and #{z}!"
end
```

This creates a command, `hello` which you can call from within Pry:

```
pry(main)> hello tom dick harry
hello there tom, dick and harry!
```

It also sets up the commands description, so that it appears in the list of `help`. For block commands the description is also used for the output of `help <command>`:

```
pry(main)> help
...
hello         Say hello to three people
...
pry(main)> help hello
Say hello to three people
```

As illustrated in the example, and explained further in <a href="#Conventions">Conventions</a>, you should use `output.puts` rather than `puts` to make your command easily re-usable in situations where Pry is not using `$stdout`.

Just as with normal commands, block commands are executed with a number of <a href="#Context_methods">Context methods</a> available.

#### Block command options

The third argument to `Pry::CommandSet#block_command` are the <a href="#Command_options">command options</a>, these define extra behaviours for commands, for example setting `keep_retval` allows you to return a value from your command:

```ruby
Pry::Commands.block_command "add-numbers", "Add a list of numbers", :keep_retval => true do |*args|
  args.map(&:to_i).inject(&:+)
end
```

This means that the output will be pretty-printed using the user's <a href="/pry/pry/wiki/Customization-and-configuration#wiki-Config_print">pretty printer</a> and more importantly, it is made available in <a href="/pry/pry/wiki/Special-Locals#wiki-Last_result">`_`</a>.

```
pry(main)> add-numbers 1 2 3 4
=> 10
pry(main)> _ + 5
=> 15
```

### Class commands

Creating a simple command is very similar to creating a block command, though a little more verbose:

```ruby
Pry::Commands.create_command "echo" do
  description "Echo the input: echo [ARGS]"

  def process
    output.puts args.join(" ")
  end
end
```

Every time the command is invoked by the user, a new instance of the command class is created, it is setup as described in the <a href="#Complete_example">Complete example</a>, and then the process method is called.

Class commands were designed to help commands introspectable, they provide a declarative syntax for setting up the `description`, and in addition to setting up `help` like a <a href="#BlockCommand">Block command</a>, class commands also respond to `--help`:

```
pry(main)> help
...
echo      Echo the input: echo [ARGS]
...
pry(main)> echo --help
Echo the input: echo [ARGS]

options:

    -h, --help      Show this message.
```

<small>NOTE: it is possible to pass the `description` and `command_options` as arguments to `create_command`, just like `block_command`.</small>

#### Handling options

The main advantage of class commands is that you can easily support command-line style options or flags. This is done by overriding the `options` method:

```ruby
Pry::Commands.create_command "echo" do
  description "Echo the input: echo [ARGS]"

  # opt is a Slop object, see https://github.com/injekt/slop/blob/master/README.md
  def options(opt)
    opt.on :u, :upcase, "Upper-case the arguments before echoing"
    opt.on :d, :downcase, "Lower-case the arguments before echoing"
  end

  def process
    string = args.join(" ")
    string.upcase! if opts.upcase?
    string.downcase! if opts.downcase?

    output.puts string
  end
end
```

As you can see there are two methods defined on commands that make it easy to handle options:

* `opts` returns the <a href="https://github.com/injekt/slop">Slop</a> object that parsed the flags.
* `args` returns any positional arguments that are left over.

Pry currently uses the <a href="https://github.com/injekt/slop">Slop</a> parser library internally, so please look at their <a href="https://github.com/injekt/slop/blob/master/README.md">README</a> for exactly what kind of options you can support.

### Complete example

This section gives a summary of all the things you can do when defining a new <a href="#Class_commands">Class Command</a> using the `echo` command from the previous section as an example.

```ruby
Pry::Commands.create_command "echo" do
  # The description is a short summary (aim for a maximum of about 80 characters).
  # It is displayed after the command's name in the list of commands output by `help`.
  #
  # If you don't set a description, the String "No description." is used.
  description "Echo the input."

  # The banner is a longer summary (aim for several lines).
  # It is displayed above the options when running `command --help` or `help command`
  # Normally the first line is a summary of the usage.
  #
  # If you don't set a banner, the description is used.
  banner <<-BANNER
    Usage: echo [ -u | -d ] [ -j <joiner> ] <words>

    Prints the words to the output, upcasing or downcasing as appropriate, separated
    by the joiner string.
  BANNER

  # The command options are explained in the Command options section, they allow
  # tweaking of the default behaviour of commands. You can normally omit them as the
  # default options will usually work.
  command_options(
    :shellwords => false
  )

  # The {setup} method is called right at the start of command invocation, before
  # {options} is called, and before {process} is called. You can use it to initialize
  # state that the rest of your command will need, please don't override {initialize}
  # itself. Every time setup is called will be on a fresh instance of the Command Class
  # so you don't need to clean up old state left by previous invocations.
  def setup
    @joiner = " "
  end

  # The {options} method is called between {setup} and {process} in order to initialize
  # the {opts} and {args}.
  #
  # It is also called (without {setup} or {process}) when displaying the help message, so
  # it should not have any side-effects.
  def options(opt)
    opt.on :u, :upcase, "Upper-case the arguments before echoing"
    opt.on :d, :downcase, "Lower-case the arguments before echoing"
    opt.on :j, :joiner, "Join arguments with a string (default: ' ')", true do |j|
      @joiner = j
    end
  end

  # The {process} method is where all the magic of your command should happen.
  # It isn't passed any options, but see the section on Context methods for an
  # overview of what it can access in addition to {opts} and {args}
  def process

    # CommandErrors can be raised when the command has gone wrong in an
    # expected way. They render nicely in the Pry REPL.
    raise Pry::CommandError, "-u doesn't make sense with -d" if opts.u? && opts.d?

    string = joined_args
    string.upcase! if opts.upcase?
    string.downcase! if opts.downcase?
    output.puts string
  end

  # You can define your own private helper methods for this command. If you want
  # to share helpers, then define them using Pry::Commands.helpers{ def foo; end }
  # instead.
  private

  def joined_args
    args.join(@joiner)
  end
end
```

### Regexp commands

Both Block Commands and Class Commands can be defined as a Regexp instead of a String. This is most useful if you want to provide fun syntax for commands. If you want to do this, then you should also set the **:listing** <a href="#Command_options">command option</a> to a String so that the output of "help" looks correct.

The capture groups of the regular expression are made available in two places, firstly by calling the `captures` method, and secondly by being added to the front of the `args` array.

```ruby
Pry::Commands.create_command /wtf([?!]*)/ do
  description "Show the exception backtrace, add more ?!s to see more lines."

  command_options :listing => "wtf?!"

  def process
    raise Pry::CommandError, "No most-recent exception" unless pry_instance.last_exception
    number_of_lines = [captures[0].size, 0.5].max * 10
    output.puts pry_instance.last_exception
    output.puts pry_instance.last_exception.backtrace.first(number_of_lines)
  end
end
```

This can then be used:

```
pry(main)> 1 / 0
ZeroDivisionError: divided by 0
pry(main)> wtf?!?
divided by 0
(pry):1:in `/'
(pry):1
```

### Command options

Command options are parameters that tweak the behaviour of commands, they are provided by calling the `command_options` method on <a href="#Class_commands">Class Commands</a>, or as the third parameter when defining <a href="#Block_commands">Block Commands</a>.

* **:keep_retval** (Boolean) — Whether or not to use return value of the block for return of command or just to return `void` (the default). If your command returns `void` even if it has :keep_retval set to true, the command will have no return value and will show no eval output (e.g `=> result`)
* **:requires_gem** (Array<String>) — Whether this command is an optional command contingent upon any gem dependencies. If it is and dependencies not met then command is disabled and a stub proc giving instructions to install command is provided. Note only use this option if you want your command to be optional; use of this option should be rare.
* **:interpolate** (Boolean) — Whether string `#{}` based interpolation is applied to the command arguments before executing the command. Defaults to true.
* **:use_prefix** (Boolean) — Whether or not the command responds to the <a href="#Command_prefix">command prefix</a> config option. Defaults to true.
* **:use_shellwords** (Boolean) — Whether the commands arguments are tokenized using <a href="http://www.ruby-doc.org/stdlib-1.9.3/libdoc/shellwords/rdoc/Shellwords.html">Shellwords</a>. Defaults to true. If false, arguments are simply split on spaces instead. Setting it to false is most useful if you want to accept arbitrary ruby as an argument, as you can re-combine the arguments before eval'ing them.
* **:listing** (String) — The listing name of the command. That is the name by which the command is looked up by `help` and by `show-source`. Necessary for regex based commands.
* **:takes_block** (Boolean) - Whether the command can take a block. A block is passed to a command by interposing a `|` character before providing the Ruby block syntax. e.g `my-command arg1 | { puts 'a block!' }`. The block is then made available inside the command via the `command_block` method. This method will return `nil` if no block was passed or `:takes_block` was `false`.

### Context methods

All commands can access a few special methods, in addition to those added to the helper module of their command sets.

#### Argument parsing

* `opts` — The options as parsed by Slop. (Class Commands only)
* `args` — The arguments that remained after Slop parsed the opts. (Class Commands only)
* `arg_string` — The string that appeared after the command name, or after the end of the regexp match for regexp commands.
* `captures` — The captures in the regexp (Regexp Commands only)
* `command_block` - The passed block (only available if `:takes_block => true` and a block was in fact passed).

#### Pry context

* `pry_instance` — The current instance of Pry that's running.
* `target` — The binding that is currently being Pry'd. Most useful for `target.eval`, which lets you eval code as though you were the user.
* `target_self` — A short-cut for `target.eval('self')`
* `eval_string` — The cumulative lines of input for multi-line input. Modifying this modifies the input buffer (used by commands such as `amend-line`)
* `output` — The current output object.

#### Command introspection

* `command_options` — The current command's options.
* `command_name` — The current command's name.
* `description` — The current command's description.
* `block` — The block in which the current command was defined.

#### Interacting with other commands

* `command_set` — The command set through which the command was invoked.
* `run` — Run another command in the current command set.
* `void` — A value indicating that a command returned no value.

### Conventions

When you define your own commands and especially if you distribute
them as plugins it is important you follow a few conventions.

**Ensure all output goes to the `output` object**

Ensuring that you use `output.puts` and not just the top-level `puts`
allows all Pry output to be redirected by the user.

Example:

```ruby
Pry::Commands.block_command "greet" do |name|
  output.puts "hello #{name}"
end
```

**Provide non-color variation**

If your command uses colored output please ensure that you provide a
non-color variation. Use the `Pry.config.color` option to determine
whether color should be used.

```ruby
Pry::Commands.block_command "greet" do |text|
  output.puts Pry.config.color ? "\e[1m#{text}\e[0m" : text
end
```

**Use hyphens not underscores for long command names**

Commands are not methods, so to further distinguish them from
methods (and avoid conflict) it is recommended to segment long names
with hyphens rather than underscores.

```ruby
Pry::Commands.block_command "long-command-name" do
  output.puts "a long name"
end
```

**Provide a `listing` for Regexp commands**

<a href="#Command_regexp">Regexp commands</a> need a 'listing' name if they are to be looked up by
commands such as `help` and `show-command`.

Example:

```ruby
Pry::Commands.block_command /\.(.*)/, "Invoke a shell command", :listing => "shell-command" do |cmd|
  system(cmd)
end

```