## Quick Menu

1. [pry-byebug](#pry-byebug) / [code](https://github.com/deivid-rodriguez/pry-byebug)
1. [pry-remote-em](#pry-remote-em) / [code](https://github.com/simulacre/pry-remote-em)
1. [pry-stack\_explorer](#pry-stack_explorer) / [code](https://github.com/pry/pry-stack_explorer)
1. [pry-theme](#pry-theme) / [code](https://github.com/kyrylo/pry-theme)
1. [pry-inline](#pry-inline) / [code](https://github.com/seikichi/pry-inline)
1. [pry-debugger-jruby](#pry-debugger-jruby) / [code](https://github.com/ivoanjo/pry-debugger-jruby)
1. [pry-shell](#pry-shell) / [code](https://github.com/meinac/pry-shell)
1. [pry-doc](#pry-doc) / [code](https://github.com/pry/pry-doc) 
1. [pry-rescue](#pry-rescue) / [code](https://github.com/ConradIrwin/pry-rescue)
1. [pry-moves](#pry-moves) / [code](https://github.com/garmoshka-mo/pry-moves)

### [Currently functional only on a fork](#currently-functional-only-on-a-fork-1)

1. [pry-coolline](#pry-coolline) / [code](https://github.com/pry/pry-coolline) [issue](https://github.com/pry/pry-coolline/issues/36#issuecomment-639927104) [working fork](https://github.com/owst/pry-coolline/tree/support_new_pry_config_api)

### [Currently non-functional](#currently-non-functional-1)

1. [pry-nav](#pry-nav) / [code](https://github.com/nixme/pry-nav) [issue](https://github.com/nixme/pry-nav/issues/35)
1. [pry-syntax-hacks](#pry-syntax-hacks) / [code](https://github.com/ConradIrwin/pry-syntax-hacks) [issue](https://github.com/ConradIrwin/pry-syntax-hacks/issues/3) / *Gem unmaintained - last update pre-2018*
1. [pry-state](#pry-state) / [code](https://github.com/SudhagarS/pry-state) [issue](https://github.com/SudhagarS/pry-state/issues/16) / *Gem unmaintained - last update pre-2018*
1. [pry-auto\_benching](#pry-auto_benching) / [code](https://github.com/estum/pry-auto_benching) [issue](https://github.com/estum/pry-auto_benching/issues/1)

### [Gem unmaintained - last update pre-2018](#gem-unmaintained---last-update-pre-2018-1)

1. [pry-remote](#pry-remote) / [code](https://github.com/mon-ouie/pry-remote)
1. [pry-vterm\_aliases](#pry-vterm_aliases) / [code](https://github.com/envygeeks/pry-vterm_aliases)
1. [pry-exception\_explorer](#pry-exception_explorer) / [code](https://github.com/pry/pry-exception_explorer)
1. [pry-em](#pry-em) / [code](https://github.com/ConradIrwin/pry-em)
1. [pry-toys](#pry-toys) / [code](https://github.com/ariabov/pry-toys)
1. [pry-macro](#pry-macro) / [code](https://github.com/baweaver/pry-macro)
1. [pry-debugger](#pry-debugger) / [code](https://github.com/nixme/pry-debugger)
1. [pry-loudmouth](#pry-loudmouth) / [code](https://github.com/sagotsky/pry-loudmouth)

## Available Plugins

### pry-byebug

![gem-logo][gem-logo] [pry-byebug](https://rubygems.org/gems/pry-nav)  
Author: [deivid-rodriguez](https://github.com/deivid-rodriguez/)  
Ruby versions: 2.0+ MRI 

Adds `next` ,`step`, `finish` and `continue` commands to Pry for stepping
through your program's code and `up`, `down` and `frame` commands for callstack
navigation.

```ruby
[1] (pry) main: 0> step

From: ./y.rb @ line 4 in Object#hello:

    1: require 'pry-byebug'
    2:
    3: def hello
 => 4:   puts 'Hello'
    5: end
    6:
    7: binding.pry
    8: hello
```
For Ruby 1.9 alternatives, checkout [pry-debugger](#pry-debugger) and
[pry-stack_explorer](#pry-stack_explorer)

[Back to top](#quick-menu)

### pry-remote-em

**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-remote-em](https://rubygems.org/gems/pry-remote-em)  
Author: [simulacre](https://github.com/simulacre)  
Ruby versions: 1.9.2+ MRI

[Pry-remote-em](https://github.com/simulacre/pry-remote-em) is a sophisticated EventMachine-based alternative to pry-remote. It adds user
authentication and SSL support along with tab-completion and paging. It also allows multiple clients to connect to the same server, and multiple servers
to run on the same computer and even within the same process. `pry-remote-em` is one of the most exciting projects in the Pry ecosystem, as it opens up
possibilities for multi-user remote-debugging/exploration, as well as educational applications. It is also just fun to interact with other programmers in
a live environment. One limitation of `pry-remote-em` at the moment is the lack of pry-nav support, but this will be added in the future.

Starting the pry-remote-em server:

```ruby
require 'pry-remote-em/server'

class Foo
  def initialize(x, y)
    binding.remote_pry_em
  end
end

EM.run { Foo.new 10, 20 }
```

[Back to top](#quick-menu)

### pry-stack_explorer

![gem-logo][gem-logo] [pry-stack_explorer](https://rubygems.org/gems/pry-stack_explorer)  
Author: [banisterfiend](https://github.com/banister)  
Ruby versions: 1.9.2+ MRI

[Pry-stack_explorer](https://github.com/pry/pry-stack_explorer)  is a powerful plugin that enables navigation of your program's call-stack. From
the point a Pry session is started, you can move up the stack through parent frames, examine state, and even evaluate code. Unlike some other debuggers,
pry-stack_explorer incurs no runtime cost and enables navigation right up the call-stack to the birth of the program. Together with the
[pry-nav](https://github.com/nixme/pry-nav) plugin, it should provide the user with a fairly complete and fast debugging experience in
Ruby 1.9.2+ MRI. Pry-stack_explorer provides the `show-stack` command as well as `up` and `down`

```ruby
[1] (pry) main: 0> show-stack

Showing all accessible frames in stack (5 in total):
--
=> #0 [method] gamma <Object#gamma()>
 #1 [method] beta <Object#beta()>
 #2 [method] alpha <Object#alpha()>
 #3 [eval] <main>
 #4 [top] <main>
```

[Back to top](#quick-menu)

### pry-theme

![gem-logo][gem-logo] [pry-theme](https://rubygems.org/gems/pry-theme)  
Author: [Kyrylo Silin](https://github.com/kyrylo/)  
Ruby versions: all

[Pry Theme](https://github.com/kyrylo/pry-theme/) plugin helps you to customize your Pry colors via `prytheme` files. It adds `pry-theme` command, which allows you to test themes on the fly and and install new ones.

Put your Pry theme in `~/.pry/themes` directory and set it up in your config:

```ruby
# ~/.pryrc
Pry.config.theme = "theme-name"
```

[Back to top](#quick-menu)

### pry-inline

![gem-logo][gem-logo] [pry-inline](https://rubygems.org/gems/pry-inline)  
Author: [seikichi](https://github.com/seikichi/)  
Ruby versions: 2.0+ MRI

[Pry Inline](https://github.com/seikichi/pry-inline)
enables the inline variables view like [RubyMine](https://www.jetbrains.com/ruby/help/inline-debugging.html).

```ruby
From: /Users/seikichi/src/book/ror-tutorial/sample_app/app/controllers/users_controller.rb @ line 54 UsersController#following:
   49: def following
   50:   @title = "Following" # @title: "Following"
   51:   @user = User.find(params[:id]) # @user: #<User id: 1751, name: "Person 68", email: "person_68@example.com", created_at: "2015-09-20 05:58:40", updat
   52:   @users = @user.followed_users.paginate(page: params[:page]) # @users: #<ActiveRecord::AssociationRelation [#<User id: 1752, name: "Person 69", email
   53:   binding.pry
=> 54:   render 'show_follow'
   55: end

[1] pry(#<UsersController>)>
```

[Back to top](#quick-menu)

### pry-debugger-jruby

![gem-logo][gem-logo] [pry-debugger-jruby](https://rubygems.org/gems/pry-debugger-jruby)  
Author: [ivoanjo](https://github.com/ivoanjo/)  
Ruby versions: JRuby 9.1.3.0+ 

[pry-debugger-jruby](https://gitlab.com/ivoanjo/pry-debugger-jruby) is a Pry debugging gem with JRuby support. Similarly to [pry-byebug](#pry-byebug) above, it adds the `next` ,`step`, `finish` and `continue` commands to Pry for stepping through your program's code and the `break` command to set breakpoints.

```ruby
    3: def hello
    4:   puts 'Hello'
    5: end
    6: 
    7: binding.pry
 => 8: hello

[1] pry(main)> step

From: y.rb @ line 4 Object#hello:

    3: def hello
 => 4:   puts 'Hello'
    5: end
```

[Back to top](#quick-menu)

### pry-shell

![gem-logo][gem-logo] [pry-shell](https://rubygems.org/gems/pry-shell)  
Author: [Mehmet Emin Inac](https://github.com/meinac)  
[Pry-shell](https://github.com/meinac/pry-shell) Allows you to control multiple Pry sessions running on multiple processes via the standalone ruby process called `pry-shell`.

First, we need to spinoff our standalone `pry-shell` application, like so;

```sh
$ pry-shell
```

Then we instantiate the Pry session which can be managed by the `pry-shell` application as follows:

```ruby
require 'pry-shell'

class Foo
  def initialize(x, y)
    binding.pry_shell
  end
end

Foo.new 10, 20
```

The Pry session will be available under the `Available Sessions` menu on `pry-shell` application.

[Back to top](#quick-menu)

### pry-doc

![gem-logo][gem-logo] [pry-doc](https://rubygems.org/gems/pry-doc)  


Pry Doc extends two core Pry commands: `show-doc` and `show-source` (aliased as
`?` and `$` respectively).

For example, in vanilla Pry it’s impossible to get the documentation for the
`loop` method (it’s a method, by the way). However, Pry Doc solves that problem.


![show-doc](https://img-fotki.yandex.ru/get/9058/98991937.13/0_9faab_68d7a43a_orig)

Let's check the source code of the `loop` method.

![show-source](https://img-fotki.yandex.ru/get/9303/98991937.13/0_9faac_aa86e189_orig)

Generally speaking, you can retrieve most of the MRI documentation and
accompanying source code. Pry Doc is also smart enough to get any documentation
for methods and classes implemented in C.

[Back to top](#quick-menu)

### pry-rescue

![gem-logo][gem-logo] [pry-rescue](https://rubygems.org/gems/pry-rescue)  

Super-fast debugging for Ruby. (See [Pry to the rescue!](http://cirw.in/blog/pry-to-the-rescue))

pry-rescue is an implementation of "break on unhandled exception" for Ruby. Whenever an
exception is raised, but not rescued, pry-rescue will automatically open Pry for you:

```ruby
$ rescue examples/example2.rb
From: /home/conrad/0/ruby/pry-rescue/examples/example2.rb @ line 19 Object#beta:
    17: def beta
    18:   y = 30
 => 19:   gamma(1, 2)
    20: end
ArgumentError: wrong number of arguments (2 for 1)
from /home/conrad/0/ruby/pry-rescue/examples/example2.rb:22:in `gamma`
[1] pry(main)>
```

[Back to top](#quick-menu)

### pry-moves

![gem-logo][gem-logo] [pry-moves](https://rubygems.org/gems/pry-moves)  

Advanced debugger for ruby with stepping back by debugging previous pieces of code, natural `next` across blocks, stepping into function by name, etc.. 


To use, invoke `pry` normally:

```ruby
def some_method
  binding.pry          # Execution will stop here.
  puts 'Hello, World!' # Run 'step' or 'next' in the console to move here.
end
```

#### Advanced example

<img src="https://user-images.githubusercontent.com/2452269/27320748-37afe7de-55a0-11e7-8b8f-ae05bcb02f37.jpg" width="377">

_Demo class source [here](https://github.com/garmoshka-mo/pry-moves/issues/1)_

###### Backtrace and call stack

You can explicitly hide frames from call stack by defining variables like this:

```ruby
def insignificant_method
  hide_from_stack = true
  something_insignificant
  yield
end
```

* `hide_from_stack` - hide this function from stack
* `pry_moves_stack_tip` -  stop on first frame above this function  
* `pry_moves_stack_end` - limits stack from bottom, not possible to step below this frame  

[Back to top](#quick-menu)

## Currently functional only on a fork

### pry-coolline

![gem-logo][gem-logo] [pry-coolline](https://rubygems.org/gems/pry-coolline)  
Author: [Mon_Ouie](https://github.com/mon-ouie)  
Ruby versions: 1.9.2+ MRI

[Pry-coolline](https://github.com/pry/pry-coolline) is a nifty little Readline replacement that takes advantage of the new stdlib io-console
library to provide live syntax highlighting for user input. It's unfortunately limited to Ruby versions 1.9.2+ and MRI, but generally works well.

[Back to top](#quick-menu)

## Gem unmaintained - last update pre-2018

### pry-remote

**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-remote](https://rubygems.org/gems/pry-remote)  
Author: [Mon_Ouie](https://github.com/mon-ouie)

[Pry-remote](https://github.com/mon-ouie/pry-remote) (along with its cousin pry-remote-em) enables you to start instances of Pry in a running
program and connect to those instances over a network or the Internet. Once connected you can interact with the internal state of the program. This
plugin comes into its own when used with tools such as [Pow](http://pow.cx/), enabling you to get a Pry session in places not normally
possible. pry-remote is also notable for having [pry-nav](https://github.com/nixme/pry-nav) support.

We set up the server as follows:

```ruby
require 'pry-remote'

class Foo
  def initialize(x, y)
    binding.remote_pry
  end
end

Foo.new 10, 20
```

[Back to top](#quick-menu)

### pry-vterm_aliases

**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-vterm_aliases](https://rubygems.org/gems/pry-vterm_aliases)  
Author: [EnvyGeeks](https://github.com/envygeeks)  
Ruby versions: 1.8.7+, 1.9+

[Pry-VTerm_Aliases](https://github.com/envygeeks/pry-vterm_aliases) brings your bash and zsh aliases into Pry as known shell commands.

[Back to top](#quick-menu)

### pry-exception_explorer

**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-exception_explorer](https://rubygems.org/gems/pry-exception_explorer)  
Author: [banisterfiend](https://github.com/banister)  
Ruby versions: 1.9.2+ MRI

[Pry-exception_explorer](https://github.com/pry/pry-exception_explorer) is an interactive error console for MRI Ruby 1.9.2+ inspired by the
[Hammertime](https://github.com/avdi/hammertime) gem, which was in turn inspired by consoles found in the Lisp and Smalltalk environments. Unlike
the Hammertime gem, we are dropped into the actual context of the exception (with full access to local state) and can even walk the stack (using
pry-stack_explorer, discussed above) to isolate the cause of the exception. Rudimentary support for some C-level exceptions is also provided and activated
with a command line switch. Another feature of pry-exception_explorer is the ability to define exactly when it kicks-in. This can be as simple as specifying
an exception type, or as sophisticated as an assertion over the entire state of the stack. The [Plymouth](https://github.com/banister/plymouth) gem
works by defining a number of stack assertions for each of the testing libraries it supports. In the example below, we configure a stack assertion so that
exception explorer starts when an ArgumentError is raised, but only if the exception context is an instance of MyClass and the parent's context is an instance
of MyCallingClass:

```ruby
EE.intercept do |frame, ex|
  ex.is_a?(ArgumentError) && frame.klass.is_a?(MyClass)) &&
  frame.prev.klass.is_a?(MyCallingClass)
end
```

However, this plugin has been deprecated in favor of
[pry-rescue](https://github.com/ConradIrwin/pry-rescue).

[Back to top](#quick-menu)

### pry-em

**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-em](https://rubygems.org/gems/pry-em)  
Author:  [Conrad Irwin](https://github.com/ConradIrwin)  
Ruby versions: all

[Pry-em](https://github.com/ConradIrwin/pry-em) adds an `em:` command which allows you to run code in an [EventMachine](http://rubyeventmachine.com/) context. It also waits for asynchronous operations to complete, and binds to the `callback` and `errback` of deferrables.

[Back to top](#quick-menu)

### pry-toys

**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-toys](https://rubygems.org/gems/pry-toys)  
Author:  [Alex Riabov](https://github.com/ariabov/)  
Ruby versions: all

[Pry Toys](https://github.com/ariabov/pry-toys/) adds easy ways to create complex throwaway Ruby object in __pry__. Now you can quickly create __Array__ of Floats:

```ruby
Array.toy(3, Float) # => [1.0, 2.0, 3.0]
```

or __Hash__ with 300 keys:    

```ruby    
Hash.toy(300) # => { a: 1, b: 2, ..., kn: 300 }
```

or __String__ with 2 words:

```ruby
String.toy(2) # => "ttttttt oooo"
```

After gem is installed, these toy / throwaway Ruby objects are going to be available for creation when __pry__ is started. To learn more about other arguments you can use to customize toy objects, click [here](https://github.com/ariabov/pry-toys/)

[Back to top](#quick-menu)

### pry-macro

**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-macro](https://rubygems.org/gems/pry-macro)  
Author:  [Brandon Weaver](https://github.com/baweaver/)  
Ruby versions: all

[Pry Macro](https://github.com/baweaver/pry-macro) allows you to record a workflow, define a command, and save macros to your .pryrc. This works with both plain ruby and pry commands.

```ruby
[1] pry(main)> record
[2] pry(main)> 1
=> 1
[4] pry(main)> ls
self.methods: inspect  to_s
locals: _  __  _dir_  _ex_  _file_  _in_  _out_  _pry_

[5] pry(main)> stop -n testing -d 'A test command!'
```

Run it like any other command:

```ruby
[6] pry(main)> testing
=> 1
self.methods: inspect  to_s
locals: _  __  _dir_  _ex_  _file_  _in_  _out_  _pry_
```

Like it? You can save it and have it automatically append to your PryRC:

```ruby
[10] pry(main)> save-macro testing
```

[Back to top](#quick-menu)

### pry-debugger

**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-debugger](https://rubygems.org/gems/pry-debugger)  
Author: [Nixme](https://github.com/nixme/)  
Ruby versions: 1.9 MRI

This is the old debugging plugin for Ruby 1.9. Still useful to aid upgrading or
for legacy code.

[Back to top](#quick-menu)

### pry-loudmouth

**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-loudmouth](https://rubygems.org/gems/pry-loudmouth)  
Author: [sagotsky](https://github.com/sagotsky/)  
Ruby versions: 2.0+ MRI

[Pry Loudmouth](https://github.com/sagotsky/pry-loudmouth) makes pry
sessions more visible.  It changes the process title and emits an alarm
character whenever the session begins.   

[Back to top](#quick-menu)

## Currently non-functional

### pry-nav

**Currently non-functional**

![gem-logo][gem-logo] [pry-nav](https://rubygems.org/gems/pry-nav)  
Author: [Gopal Patel](https://github.com/nixme)  
Compatible with MRI >= 2.1.0, JRuby >= 9.1.3.0.

Teaches [Pry][pry] about `step`, `next`, and `continue` to create a simple
debugger.

To use, invoke `pry` normally:

```ruby
def some_method
  binding.pry          # Execution will stop here.
  puts 'Hello, World!' # Run 'step' or 'next' in the console to move here.
end
```

[Back to top](#quick-menu)

### pry-syntax-hacks

**Currently non-functional**  
**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-syntax-hacks](https://rubygems.org/gems/pry-syntax-hacks)  
Author: [Conrad Irwin](https://github.com/ConradIrwin)  
Ruby versions: all

[Pry-syntax-hacks](https://github.com/ConradIrwin/pry-syntax-hacks) adds some syntactic sugar to Pry. Most usefully it allows you to look at instance variables of objects: `user.@password`, call private/protected methods on objects: `user.!hash_password('test')`. It also lets you access methods: `passwords.map user.&hash_password`, and access variables in previously active pry bindings (a.la. `cd`): `puts ../a`.

[Back to top](#quick-menu)


### pry-state

**Currently non-functional**  
**Gem unmaintained - last update pre-2018**

![gem-logo][gem-logo] [pry-state](https://rubygems.org/gems/pry-state)  
Author: [Sudhagar](https://github.com/SudhagarS/)  
Ruby versions: 2.0+ MRI 

[Pry State](https://github.com/SudhagarS/pry-state) lets you can see the values of the instance and local variables in a pry session.

```ruby
[1] pry(#<UsersFetcherService>)> n

From: /Users/sudhagars/Projects/authentication-service/app/services/users_fetcher_service.rb @ line 17 UsersFetcherService#fetch:

    11: def fetch
    12:   a = 'dummy string'
    13:   b = ['dummy', 'array']
    14:   binding.pry
    15:   apply_access_type_filter
    16:   apply_third_party_filter
 => 17:   reject_protected_auth
    18:   sort_auths
    19:   auths
    20: end

@user                    #<User:0x007fd7da194790>
@access_type_filter      "owner"
@third_party_filter      len:0 []
@auths                   len:3 [#<Auth:0x007fd7d6fb55a8 @new_record=false, @attrib...
a                        "dummy string"
b                        len:2 ["dummy", "array"]
```

[Back to top](#quick-menu)

### pry-auto_benching

**Currently non-functional**

![gem-logo][gem-logo] [pry-auto_benching.rb](https://rubygems.org/gems/pry-auto_benching.rb)  
Author: [r-obert](https://github.com/r-obert/); resurrected by [estum](https://github.com/estum/)  
Ruby versions: 2.1+ MRI

[pry-auto_benching.rb](https://github.com/estum/pry-auto_benching) automatically benchmarks input 
given to Pry.<br>

```
[7] pry(main)> sleep 0.2
=> 0
[8] 0.2s (main)> Net::HTTP.get_response URI.parse('https://github.com')
=> #<Net::HTTPOK 200 OK readbody=true>
[9] 1.34s (main)> Net::HTTP.get_response URI.parse('https://www.ruby-lang.org')
=> #<Net::HTTPOK 200 OK readbody=true>
[10] 1.53s (main)>
```

[Back to top](#quick-menu)

[gem-logo]: ./images/rubygems_logo.svg
