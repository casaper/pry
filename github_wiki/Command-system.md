### Quick Menu

* [Invoking commands](#invoking-commands)
* [The `help` command](#the-help-command)
* [The command prefix](#the-command-prefix)
* [Command Interpolation](#command-interpolation)
* [Custom commands](#custom-commands)
* [Command sets](#command-sets)

### Overview

The command system is a unique feature that sets Pry apart from other
REPLs. Pry commands are not methods; instead they
are special strings that are interpreted directly by Pry before the input buffer is evaluated. This
approach was chosen because:

* Commands can do things that methods cannot do: such as modifying 
  the code that the user has typed.
* Command syntax is more intuitive, and commands can easily support
  flags like `--help` that users expect.
* Commands are local to the Pry console, they don't require global
  monkey-patches, but they can still be used no-matter which object you are
  Prying.

As Commands are implemented in Ruby, there's an endless number of things that
they can be used to do. At the extreme end of the spectrum, the [pry_nav](https://github.com/nixme/pry-nav)
plugin adds "step", and "next" commands for walking through Ruby code. A more
simple example is provided by the [pry-highlight](https://github.com/ConradIrwin/pry-highlight) plugin,
which adds a single "highlight" command to pretty-print JSON and XML.

### Invoking commands

Simply enter the name of the command at the prompt, followed by any arguments
that it takes, and it will be run:

```ruby
pry(main)> ls -l
locals: _  _ex_  _pry_  _in_  _out_  _file_  _dir_
pry(main)>
```

In the rare case that the name of a command conflicts with some ruby
that you would like to execute, you can prefix the ruby code with a semicolon
to prevent commands from being run: 

```ruby
pry(main)> ;ls = "hello"
=> "hello"
pry(main)> ;ls
=> "hello"
```

<small>HINT: if you find this happens to you a lot, consider setting the [[Command prefix|Command-system#wiki-Command_prefix]].</small>

[Back to top](#quick-menu)

### The `help` command

The help command outputs a list of all commands currently defined in Pry, 
along with a short description of what that command does:

```ruby
pry(main)> help
...
gist               Gist a method or expression history to github. Type `gist --help` for more info.
help               Show a list of commands, or help for one command
hist               Show and replay Readline history. Aliases: history
...
```

The help command can also be used to show more details about each individual command,
as explained on the [[Live help|Live-help#wiki-Pry_command_docs]] page.

[Back to top](#quick-menu)

### The command prefix

As stated above, command evaluation takes precedence over normal Ruby
code execution. This precedence order should not normally be an issue, but if turns out
to be, there are a couple good options.

For a quick fix, you can prefix any line with a semicolon (or any other valid Ruby syntax) to make that line bypass interpret as a command.

Example:

```ruby
From: /home/rking/shared/src/scrap/foo.rb @ line 3 Frog#jump:
    3: def jump
    4:   ls = 22
 => 5:   binding.pry
    6: end
[1] pry(#<Frog>)> ls
Frog#methods: jump
locals: _  __  _dir_  _ex_  _file_  _in_  _out_  _pry_  ls
[2] pry(#<Frog>)> ;ls
22
```

As a broader solution, you can set a [`Pry.config.command_prefix`](Customization-and-configuration#wiki-Config_prefix), which is a short string that must be prepended before a command to further disambiguate it from actual Ruby code.

Note that two commands ignore this prefix for convenience: [shell
commands](Shell-integration#wiki-Execute_shell_commands)
(strings prefixed by a `.`) and the ['clear input
buffer'
command](User-Input#wiki-Clear_input) (`!`).

The `Pry.config.command_prefix` option defaults to `""` (no prefix).

Example:

```ruby
pry(main)> Pry.config.command_prefix = "%"
=> "%"
pry(main)> ls -l
NameError: undefined local variable or method `l' for main:Object
from (pry):2:in `<main>'
pry(main)> %ls
locals: _  _ex_  _pry_  _in_  _out_  _file_  _dir_
pry(main)>
```

[Back to top](#quick-menu)
<a name="Command_interpolation">
### Command Interpolation

As discussed cursorily in [accessing the system
shell](https://github.com/pry/pry/wiki/Shell-Integration#wiki-Execute_shell_commands) you can
directly interpolate Ruby code into commands through the use of the
standard `#{}` syntax:

```
pry(main)> dir = File.dirname(log_file)
=> "/var/log"
pry(main)> .cd #{dir}
pry(main)> .pwd
/var/log
```

This is particularly useful when combined with Pry's [[special locals]]:

```
pry(main)> show-doc Pry#rep
From: /home/conrad/.rvm/gems/ree-1.8.7-2011.03@pry/gems/pry-0.9.8/lib/pry/pry_instance.rb @ line 210:
Number of lines: 5
Owner: Pry
Visibility: public
Signature: rep(*arg1)

Perform a read-eval-print.
If no parameter is given, default to top-level (main).
param [Object, Binding] target The receiver of the read-eval-print
example
  Pry.new.rep(Object.new)

pry(main)> cat #{_file_}
require "pry/indent"

class Pry
...
...
```

[Back to top](#quick-menu)
<a name="Custom_commands">
### Custom commands

It is of course possible to add commands to Pry itself, either at runtime or in your [~/.pryrc](Pry-rc),
for a full guide to the various possibilities, see the [[custom commands]] page. To whet your appetite,
here are some simple examples:

**First command**

For our first command let's replicate the Bash shell syntax for
replaying a line of history: `!<history_line>`

```ruby
Pry.commands.block_command(/!(\d+)/, "Replay a line of history", :listing => "!hist") do |line|
  run "history --replay #{line}"
end
```

Let's now use it:

```
pry(main)> hist --tail 3
893: puts 'midnight, beauty, vision, dies.'
894: ls -m
895: ls -i
pry(main)> !893
midnight, beauty, vision, dies.
=> nil
pry(main)>
```

**Second command**

For our second command let's implement the sed tool's `s/expr1/expr2`
substitution syntax.

```ruby
Pry.commands.block_command /s\/(.*?)\/(.*?)/ do |source, dest|
  eval_string.gsub!(/#{source}/) { dest }
  run "show-input"
end
```

Now let's see how it's used:

```
pry(main)> def hello(friends)
pry(main)*   puts "my friends i have come here to say hello"
pry(main)*   puts "hello is an unusual word, but is appropriate at a time like this"
pry(main)*   puts "when it has been so long since we last met"
pry(main)*   puts "before the dogs were put to sleep"
pry(main)* s/hello/goodbye
1: def goodbye(friends)
2:   puts "my friends i have come here to say goodbye"
3:   puts "goodbye is an unusual word, but is appropriate at a time like this"
4:   puts "when it has been so long since we last met"
5:   puts "before the dogs were put to sleep"
pry(main)* s/dogs/cats
1: def goodbye(friends)
2:   puts "my friends i have come here to say goodbye"
3:   puts "goodbye is an unusual word, but is appropriate at a time like this"
4:   puts "when it has been so long since we last met"
5:   puts "before the cats were put to sleep"
pry(main)* end
=> nil
```

[Back to top](#quick-menu)

### Command sets

Internally, commands are organised into command sets; and once you've mastered the basics of
[[creating your own commands|custom commands]], you'll want to start organising them.

In all the examples before we've used `Pry::Commands` to define commands, as that is
the default command set that is currently active in the REPL. You can also bundle
your commands into their own `CommandSet`:

Example:

```ruby
command_set = Pry::CommandSet.new do
  block_command "greet" do |name|
    output.puts "hello #{name}"
  end

  block_command "add5" do |num|
    output.puts "#{num.to_i + 5}"
  end
end
```

Command sets also allow you to define helper modules. These are
modules with auxiliary methods for use by all commands in that
set:

Example:

```ruby
command_set = Pry::CommandSet.new do
  block_command "greet" do |name|
    greet_person(name)
  end

  block_command "greet-pair" do |n1, n2|
    greet_person([n1, n2].join(" and "))
  end

  helpers do
    def greet_person(name)
      output.puts "hello #{name}"
    end
  end
end
```

Once you have created a Commandset, the chances are that you want to make it
available to the user. To do this:

```ruby
Pry::Commands.import my_command_set
```

CommandSet's have various other methods for renaming, removing, importing and
otherwise dicing the commands that they contain, for full information please
refer to the [YARD documentation](http://rubydoc.info/github/pry/pry/Pry/CommandSet).

[Back to top](#quick-menu)
