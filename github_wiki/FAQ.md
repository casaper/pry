### Quick Menu:

* <a href="#awesome_print">How can I use awesome_print with Pry?</a>
* <a href="#readline_threads">Threads don't work, what's wrong?</a>
* <a href="#readline">How can I get readline support (ctrl+r etc) for Pry in OSX?</a>
* <a href="#clear_screen">How do I clear the screen in a Pry session?</a>
* <a href="#hirb">How can I use the Hirb gem with Pry?</a>
* <a href="#pow">How can I use Pry with pow?</a>
* <a href="https://github.com/pry/pry/wiki/Setting-up-Rails-or-Heroku-to-use-Pry">Using Pry with Rails</a>
* <a href="#delete_key">Why is ~ printed when I type the delete key?</a>
* <a href="#emacsshell">Why is my emacs shell output showing odd characters?</a>
* <a href="#etoomuch">What if the output of a line is going to dump a huge object?</a>
* <a href="#commands_mix">How can I ignore commands on incompleted lines?</a>
* <a href="#clipit">Does Pry have an easy way to copy to clipboard instead of to Gist?</a>
* <a href="#ruby191">Why doesn't Pry work with Ruby 1.9.1?</a>
* <a href="#disable_pry">How do I stop executing a `binding.pry` call in a loop?</a>
* <a href="#pager">How do I use other pager than `less`?</a>
* [How do I use `less` on Windows ?](#windows-less)
* <a href="#bindingpry">`binding.pry` is a function of the `pry-rails` gem, yeah?</a>

[Back to Main Menu](/pry/pry/wiki)

<a name="awesome_print"></a>
### How can I use awesome_print with Pry?
If you miss awesome_print's way of formatting output, here's how you can use it in place of the formatting which comes with pry.

Append this code to `.pryrc`.

```ruby
begin
  require 'awesome_print'
  Pry.config.print = proc { |output, value| output.puts value.ai }
rescue LoadError => err
  puts "no awesome_print :("
end
```

If you would like to use Pry's pager with awesome_print a slight modification to the above is needed:

```ruby
begin
  require 'awesome_print'
  Pry.config.print = proc { |output, value| Pry::Helpers::BaseHelpers.stagger_output("=> #{value.ai}", output) }
rescue LoadError => err
  puts "no awesome_print :("
end
```

<a name="readline_threads"></a>
### Threads don't work, what's wrong?
Some systems (Mac OS X in particular) use Editline instead of GNU Readline. The Ruby Readline library currently has an issue when compiled with the Editline library which makes it block all threads rather than just the one calling Readline.readline() (it blocks while holding the global VM lock, preventing other threads from acquiring it). This can be fixed by installing [GNU Readline on OS X]
(https://github.com/guard/guard/wiki/Add-Readline-support-to-Ruby-on-Mac-OS-X).

An alternative is using https://github.com/luislavena/rb-readline, a pure-Ruby implementation of Readline that does not suffer the threading issue, though this has other issues.

<a name="readline"></a>
### How can I get readline support (ctrl+r etc) for Pry in OSX?
Technically for OSX you don't need readline, and we'll focus on using libedit which OSX comes with instead.

Edit the file `.editrc` in your home directory. Add this line:
```
bind "^R" em-inc-search-prev
```

You can now use ctrl+r to do a reverse history search.

Ref: http://hints.macworld.com/article.php?story=20080313113705760

Alternatively, install GNU Readline and get Ruby to use it: https://github.com/guard/guard/wiki/Add-Readline-support-to-Ruby-on-Mac-OS-X

Finally, Pry does work with rb-readline, a pure Ruby implementation of Readline which shouldn't suffer from the platform and install dependencies of the standard Readline.
Ref: https://github.com/luislavena/rb-readline


<a name="clear_screen"></a>
### How do I clear the screen in a Pry session?

You can either use `^L` (control-l) or use Pry's [shell integration features](https://github.com/pry/pry/wiki/Shell-Integration) to accomplish this:

In *nix systems:

```
pry(main)> .clear
```

In Windows systems:

```
pry(main)> .cls
```

<a name="hirb"></a>
### How can I use the Hirb gem with Pry?

To use [Hirb](https://github.com/cldwalker/hirb) with Pry add the following code to your `.pryrc` file:

```ruby
begin
  require 'hirb'
rescue LoadError
  # Missing goodies, bummer
end

if defined? Hirb
  # Slightly dirty hack to fully support in-session Hirb.disable/enable toggling
  Hirb::View.instance_eval do
    def enable_output_method
      @output_method = true
      @old_print = Pry.config.print
      Pry.config.print = proc do |*args|
        Hirb::View.view_or_page_output(args[1]) || @old_print.call(*args)
      end
    end

    def disable_output_method
      Pry.config.print = @old_print
      @output_method = nil
    end
  end

  Hirb.enable
end
```

If you'd rather not have a monkey patch to maintain, the following will work as well, without the ability to disable Hirb during a session:

```ruby
require 'hirb'

Hirb.enable

old_print = Pry.config.print
Pry.config.print = proc do |*args|
  Hirb::View.view_or_page_output(args[1]) || old_print.call(*args)
end
```
<a name="pow"></a>
### How can I use Pry with pow?

Pry now supports remote sessions with the [pry-remote](https://github.com/mon-ouie/pry-remote) gem.

After installing `pry-remote` and requiring it in your code simply use `binding.remote_pry` (instead of the usual `binding.pry`) and connect to the session from another terminal using `pry-remote`. See the [pry-remote](https://github.com/mon-ouie/pry-remote) project page for more information.

<a href="#user-content-quick-menu">Back to top</a>

<a name="delete_key"></a>
### Why is `~` printed when I type the `delete` key?

#### Regular OS X Terminal

* Go to Terminal > Preferences > Settings > Keyboard
* Click `Forward Delete`
* Select `Send String to Shell`
* Press `CTRL + ALT + d`
* You want to see `\004`

#### iTerm2

* Go to iTerm > Preferences... > Keys
* Add a Global Shortcut Key
* Type the `delete` key as Shortcut (NOTE: this requires typing fn+delete on mac keyboards).
* Select Send Hex Codes as Action and type 0x004
* Click on `OK` and you're good to go

#### iTerm

Take a look at [this article](http://www.21croissants.com/howto/fix-forward-delete-key-on-snow-leopard-for-irb) (works for Lion too, but you should use [iTerm2](http://www.iterm2.com/)).


#### Build ruby with readline

(can somebody confirm this for iTerm too?)
The solution proposed for the regular OS X Terminal might have the side effect that your `delete` key will issue `CTRL + d` (same as when you type `exit`) when there is nothing on the current line.

Ruby on OS X will use libedit instead of libreadline by default. Building Ruby with readline usually solves the problem. The obvious downside of this solution is that you might have to reinstall your gems. For rbenv/ruby-build you can use something like this:
```
brew install readline && CONFIGURE_OPTS="--with-readline-dir=$(brew --prefix readline)" rbenv install <your version, e.g: 1.9.3-p194>
```

<a name="emacsshell"></a>
### Why is my emacs shell output showing odd characters?

If you run pry within Emacs' `M-x shell` and see output similar to this

```shell
$ pry
input>
[1A[0Ginput> [1B[0Ginput>
```

There are ansi codes embedded in the `auto_indent` that emacs' shell does not render properly. To clean this up add the following to your `.pryrc`

```ruby
Pry.config.auto_indent = false
```

<a href="#user-content-quick-menu">Back to top</a>

<a name="etoomuch"></a>
### What if the output of a line is going to dump a huge object?

Make a `;` the last char on the line, and that line will output nothing.

<a href="#user-content-quick-menu">Back to top</a>

<a name="commands_mix"></a>
### How can I ignore commands on incompleted lines?

An example is:
```ruby
[1] pry(main)> class Example1
[1] pry(main)*   def exit
[1] pry(main)*     exit
```

In this example, the *Pry* command `exit` is being called, not the Ruby one. Such precedence of Pry commands over Ruby (when in conflict) preserves the functionality of commands like `edit`, `amend-line`, `play`, and even `show-source`, which are pretty cool.

0. Prefix a semicolon (or space) before you type the line
0. Disable the annoying commands (`Pry.commands.delete 'exit'`) in your `~/.pryrc`
0. Rename the annoying commands (`Pry.commands.rename_command '%exit', 'exit'`)
0. Use a Pry.config.command_prefix
0. Turn on command collision warnings (`Pry.config.collision_warnings = true`)

<a href="#user-content-quick-menu">Back to top</a>

<a name="clipit"></a>
### Does Pry have an easy way to copy to clipboard instead of to Gist?

Yes, via the pry-jist gem. There is `gist --clip` command (aliased to `clipit`). 

Given a class:

```ruby
class Foo
  def bar
    :bar
  end
end
```

```
pry(main)> clipit Foo#bar
Copied content to clipboard!
```

You can also copy the whole class

```
pry(main)> clipit Foo
Copied content to clipboard!
```

<a href="#user-content-quick-menu">Back to top</a>

<a name="ruby191"></a>
### Why doesn't Pry work with Ruby 1.9.1?

Ruby 1.9.1 is not supported. The way that `class_eval` and `instance_eval` works in 1.9.1 is anomalous and different to anything in 1.8 or 1.9.2 and higher. Try to upgrade your Ruby to 1.9.3.

Link for curious people: [Ruby 1.9.1: uninitialized constant Pry::CommandSet::Ls (NameError)](https://github.com/pry/pry/issues/209)

<a href="#user-content-quick-menu">Back to top</a>

<a name="disable_pry"></a>
### How do I stop executing a `binding.pry` call in a loop?

Let's consider the following example.

```ruby
def magic_loop
  5.times do |i|
    puts "Iteration #{ i + 1 }"
    binding.pry
  end

  puts "Sorry, no more magic left for today!"
end
```

Pay attention to the line with `binding.pry` invocation. Let's run the code.

```
[2] pry(main)> magic_loop
Iteration 1

From: (pry) @ line 4 Object#magic_loop:

    1: def magic_loop
    2:   5.times do |i|
    3:     puts "Iteration #{ i + 1 }"
 => 4:     binding.pry
    5:   end
    6:
    7:   puts "Sorry, no more magic left for today!"
    8: end

[1] pry(main)> exit
Iteration 2

From: (pry) @ line 4 Object#magic_loop:

    1: def magic_loop
    2:   5.times do |i|
    3:     puts "Iteration #{ i + 1 }"
 => 4:     binding.pry
    5:   end
    6:
    7:   puts "Sorry, no more magic left for today!"
    8: end

[1] pry(main)> exit
Iteration 3

From: (pry) @ line 4 Object#magic_loop:

    1: def magic_loop
    2:   5.times do |i|
    3:     puts "Iteration #{ i + 1 }"
 => 4:     binding.pry
    5:   end
    6:
    7:   puts "Sorry, no more magic left for today!"
    8: end
```

Meh, I cannot exit the loop quickly. I have to type `exit` (or press `^D`) 5 times! Keep calm, because there is a simple solution. 

The `raise-up` command will raise an exception to the containing pry instance (RuntimeError if you give it no arguments.)  Simply type `raise-up` and find yourself out of the loop.  It also works if you want to raise an exception that will not be caught without terminating the entire REPL (as in RSpec.)

Alternatively, you can use the `disable-pry` command.

```
[1] pry(main)> disable-pry
Iteration 4
Iteration 5
Sorry, no more magic left for today!
=> nil
[3] pry(main)>
```

Great! Check the description of the command with `help disable-pry`. Now, let's try to run the method one more time.

```
[3] pry(main)> magic_loop
Iteration 1
Iteration 2
Iteration 3
Iteration 4
Iteration 5
Sorry, no more magic left for today!
=> nil
[4] pry(main)>
```

Wait, what? How do I re-enable Pry? Although there is no `enable-pry` command in Pry, there is an easy way to add it: you can roll up your own command. Add this to your `~/.pryrc`.

```ruby
# ~/.pryrc
Pry::Commands.block_command('enable-pry', 'Enable `binding.pry` feature') do
  ENV['DISABLE_PRY'] = nil
end
```

Now, just execute the freshly added command and try to run `magic_loop` again.

Web developers may find it useful to simply call `ENV['DISABLE_PRY'] = nil` before every request, in a `before_filter` or whatever their framework of choice provides. As it is just an environment variable, it will not risk breaking anything.

There is a faster but blunt way to exit a loop containing pry calls; exit the process by running `Process.exit`. Note that your ruby instance will be terminated and you will lose any unsaved data.

<a href="#user-content-quick-menu">Back to top</a>

<a name="pager"></a>
### How do I use other pager than `less`?

Just set the `PAGER` environment variable. For example:

```
% PAGER="my-pager" pry
[1] pry(main)>
```

<a href="#user-content-quick-menu">Back to top</a>

### <a id="windows-less">How do I use `less` on Windows?</a>

The default native pager on Windows may not be nice to use over the long term, but
you can install and run `less` natively on Windows without much fuss.

The easiest way is through the [`chocolatey`](https://chocolatey.org/) package manager. 
After chocolatey is installed, you can install `less` with the following command from an 
Administrator terminal:

    choco install less
 
Then start Pry and `less`  will be the default pager. Enjoy :)

<a href="#user-content-quick-menu">Back to top</a>

<a name="bindingpry"></a>
### `binding.pry` is a function of the `pry-rails` gem, yeah?

Nope. It's a part of Pry. If you want to learn more about it, read the article called [So what is “binding.pry” exactly](http://kyrylo.org/posts/so-what-is-binding-pry-exactly).

<a href="#user-content-quick-menu">Back to top</a>