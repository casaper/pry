Exception handling
==================

<a name="Back_to_top"></a>
### Quick Menu:

* <a href="#__ex__">The `_ex_` local variable</a>
* <a href="#wtf">Using the `wtf?` command</a>
* <a href="#cat___ex">Using the `cat --ex` command</a>
* <a href="#Exception_window_size">Configuring `cat --ex`</a>
* <a href="#edit___ex">Using the `edit --ex` command</a>
* <a href="#Exception_handler">Customising the exception handler</a>
* <a href="#Exception_whitelist">Configuring which exceptions are caught</a>
* <a href="#Exception_explorer">The pry-rescue plugin</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

Pry catches most of the exceptions that your code raises so that you have time to work out what went wrong and fix the problem.

<a name="__ex__"></a>
### The `_ex_` local variable

Pry gives you access to the most recently caught exception in a local variable called `_ex_`. Unlike Ruby's builtin `$!`, this persists until another exception is raised, so can be used for detailed digging.

The `_ex_` variable is a 'special local', read more about those [here](https://github.com/pry/pry/wiki/Special-Locals).

For example:

    pry(main)> 4/0
    ZeroDivisionError: divided by 0
    from (pry):1:in `/'

    pry(main)> _ex_.message
     => "divided by 0"

    pry(main)> puts _ex_.backtrace
    (pry):1:in `/'
    (pry):1

<a href="#Back_to_top">Back to top</a>

<a name="wtf"></a>
### Using the `wtf?` command

As an alternative to using `_ex_.backtrace` you can use the `wtf?` command to display a few lines of the backtrace for the most recent exception. If you want to see more lines, **add more question marks or exclamation marks**.

Example: Using `wtf` to investigate the very top of the backtrace

```
pry(main)> sdf
NameError: undefined local variable or method `sdf' for main:Object
from (pry):17:in `__pry__'
pry(main)> wtf
Exception: NameError: undefined local variable or method `sdf' for main:Object
--
 0: (pry):17:in `<main>'
 1: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:251:in `eval'
 2: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:251:in `re'
 3: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:218:in `rep'
 4: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:202:in `block (2 levels) in repl'
```

Example: Append `?` to see more of the backtrace

```
pry(main)> sdf
NameError: undefined local variable or method `sdf' for main:Object
from (pry):17:in `__pry__'
pry(main)> wtf?
 0: (pry):17:in `<main>'
 1: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:251:in `eval'
 2: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:251:in `re'
 3: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:218:in `rep'
 4: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:202:in `block (2 levels) in repl'
 5: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:201:in `loop'
 6: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:201:in `block in repl'
 7: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:200:in `catch'
 8: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:200:in `repl'
 9: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_class.rb:136:in `start'
```

Example: Append even more `?` or `!` marks to see everything

```
pry(main)> sdf
NameError: undefined local variable or method `sdf' for main:Object
from (pry):17:in `__pry__'
pry(main)> wtf?!?!!!
NameError: undefined local variable or method `sdf' for main:Object
from (pry):17:in `__pry__'
 0: (pry):17:in `<main>'
 1: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:251:in `eval'
 2: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:251:in `re'
 3: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:218:in `rep'
 4: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:202:in `block (2 levels) in repl'
 5: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:201:in `loop'
 6: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:201:in `block in repl'
 7: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:200:in `catch'
 8: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_instance.rb:200:in `repl'
 9: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/pry_class.rb:136:in `start'
10: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/cli.rb:150:in `block in <top (required)>'
11: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/cli.rb:59:in `call'
12: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/cli.rb:59:in `block in parse_options'
13: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/cli.rb:59:in `each'
14: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/lib/pry/cli.rb:59:in `parse_options'
15: /Users/john/.rvm/gems/ruby-1.9.3-p125/gems/pry-0.9.8.3/bin/pry:16:in `<top (required)>'
16: /Users/john/.rvm/gems/ruby-1.9.3-p125/bin/pry:19:in `load'
17: /Users/john/.rvm/gems/ruby-1.9.3-p125/bin/pry:19:in `<main>'
```

To see the entire backtrace, pass the `-v` (`--verbose`) flag, e.g: `wtf -v`

<a href="#Back_to_top">Back to top</a>
<a name="cat___ex"></a>
### Using the `cat --ex` command

In addition to giving you easy access to the backtrace, Pry can also show you the actual code that caused problems. This is done with the `--ex` argument to the [cat command](Shell-Integration#wiki-Cat).

For example:

    [1] pry(main)> require 'set'
    => true
    [2] pry(main)> Set.new(1)
    ArgumentError: value must be enumerable
    from /home/pryer/ruby/lib/ruby/1.8/set.rb:263:in `merge'
    [3] pry(main)> cat --ex

    Exception: ArgumentError: value must be enumerable
    --
    From: /home/pryer/ruby/lib/ruby/1.8/set.rb @ line 263 @ level:  0 of backtrace (of 3).

       258:   # returns self.
       259:   def merge(enum)
       260:     if enum.is_a?(Set)
       261:       @hash.update(enum.instance_eval { @hash })
       262:     else
     =>263:       enum.is_a?(Enumerable) or raise ArgumentError, "value must be enumerable"
       264:       enum.each { |o| add(o) }
       265:     end
       266: 
       267:     self
       268:   end

This is partly useful, I know now that the code I'm calling is explicitly expecting an Enumerable, but I didn't give it one. What's not so clear is where it's getting that Enumerable from — after all, I didn't call `Set#merge`! Luckily `cat --ex` also supports an optional argument, `cat --ex N` jumps N lines up the backtrace. To find out how my code ended up in `Set#merge` I can use `cat --ex 1`:

    [4] pry(main)> cat --ex 1

    Exception: ArgumentError: value must be enumerable
    --
    From: /home/pryer/ruby/lib/ruby/1.8/set.rb @ line 76 @ level:  1 of backtrace (of 3).

       68:   def initialize(enum = nil, &block) # :yields: o
       69:     @hash ||= Hash.new
       70: 
       71:     enum.nil? and return
       72: 
       73:     if block
       74:       enum.each { |o| add(block[o]) }
       75:     else
     =>76:       merge(enum)
       77:     end
       78:   end

It's now clear why the exception happened, and exactly what I need to do to fix it.

<small>HINT: If you're having problems with the standard library, you should probably use the [show-doc command](Documentation-browsing#wiki-Show_doc) command to read up on what you're doing wrong! In this case `show-doc Set#initialize` tells me it should take an `Enumerable`, not a number.</small>

##### Use with `wtf?`

It can be effective to use this command in concert with the `wtf?` command. Use `wtf?` to show the backtrace and `cat --ex N` to show the context for a frame in that backtrace.

<a href="#Back_to_top">Back to top</a>

<a name="Exception_window_size"></a>
#### Configuring `cat --ex`

[`cat --ex`](#cat___ex) usually shows five lines of context before, and five lines of context after the exception. You can override this by setting `Pry.config.default_window_size` in your [~/.pryrc](Pry-rc).

For example:

```ruby
Pry.config.default_window_size = 10
```

This will show you a total of 21 lines of context: 10 before, 10 after and the actual line contained within the exception's backtrace.

<a href="#Back_to_top">Back to top</a>

<a name="edit___ex"></a>
### Using the `edit --ex` command

In the previous section, we saw how `cat --ex` can be used to show you how the error came about. In that case it was because we used the library wrong. Sometimes however, the problem is caused by the code being called. In that case we want to be able to fix the code as quickly as possible.

To help you do this, Pry's  [edit command](Editor-Integration#wiki-Edit_command) also supports `--ex` in the same manner as `cat`. It also takes an optional number to jump up the stacktrace:

    [1] pry(main)> require 'set'
    => true
    [2] pry(main)> Set.new(1)
    ArgumentError: value must be enumerable
    from /home/pryer/ruby/lib/ruby/1.8/set.rb:263:in `merge'
    [3] pry(main)> edit --ex 1 # N.B. do not try editing the standard library at home!
    [4] pry(main)> Set.new(1)
    => #<Set: {1}>

Just like with `cat --ex`, if you provide a number to `edit --ex N` it will jump N levels up the stacktrace, and if you don't specify N (or set it to 0) it will edit the actual line of code that raised the exception.

<a href="#Back_to_top">Back to top</a>

<a name="Exception_handler"></a>
### Customising the Exception handler

By default when Pry catches an exception, it doesn't show you very much information — it only includes the message, and the first line of the backtrace. If you'd prefer to see more information without having to use additional commands, you can override [`Pry.config.exception_handler`](Customization-and-configuration#wiki-Config_exception) in your [~/.pryrc](Pry-rc).

For example, to show a few more lines of backtrace, you might want to do:

```ruby
Pry.config.exception_handler = proc do |output, exception, _pry_|
  output.puts "#{exception}"
  output.puts "#{exception.backtrace.first(10)}"
end
```

Or, if you find that you always use `cat --ex` after an exception is raised, you can short-cut by getting Pry to run that for you:

```ruby
Pry.config.exception_handler = proc do |output, exception, _pry_|
  _pry_.run_command 'cat --ex'
end
```

Because the exception_handler is designed to deal with exceptions thrown by the code that you are currently prying, it should be reasonably robust. For example, it would be silly to have an exception_handler that itself threw an exception. If you do end up throwing an exception by accident, Pry will still catch it and tell you, for example:

      (pry) output error: #<ArgumentError, too few arguments>

The other thing to remember is that you need to call `output.puts`, the return value of your Proc is ignored, and just using `puts` will send output to `$stdout` instead of `output`.

<a href="#Back_to_top">Back to top</a>

<a name="Exception_whitelist"></a>
### Configuring which exceptions are caught

Pry tries to be clever about which exceptions are caught. While it tries to catch everything that your program might throw by accident, it tries not to catch exceptions that are thrown deliberately. The most obvious example of a deliberate exception is `SystemExit`, which is thrown when you call `Kernel#exit`, though Pry also allows all `SignalException`s to be passed through so that you can run the UNIX `kill` command on instances of Pry.

If your program has its own exceptions that should never be caught by Pry, you can add them to  [`Pry.config.exception_whitelist`](Customization-and-configuration#wiki-Config_exception_whitelist) in your [~/.pryrc](Pry-rc). For example to tell Pry not to catch out-of-memory errors:

```ruby
Pry.config.exception_whitelist = [SignalError, SystemExit, NoMemoryError]
```

If you find a class of exception that you think Pry should not catch by default, please open a [bug report](/pry/pry/issues) and let us know.

<a name="Exception_explorer"></a>
### The pry-rescue plugin

For much more powerful exception handling, look into the [pry-rescue](https://github.com/conradirwin/pry-rescue) plugin.

<a href="#Back_to_top">Back to top</a>