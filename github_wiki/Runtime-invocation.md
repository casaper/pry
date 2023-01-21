# Runtime invocation

<a name="Back_to_top">

### Quick Menu:

* <a href="#Invoke_on_binding">Invoking on a binding</a>
* <a href="#Invoke_on_object">Invoking on an object</a>
* <a href="#Customize_session">Customizing your runtime session</a>
* <a href="#Whereami">The `whereami` command</a>
* <a href="#Pry_backtrace">The `pry-backtrace` command</a>
* <a href="#Runtime_cases">Runtime use cases</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

Aside from using Pry as an IRB-alternative it also has the ability to function as a developer console and lightweight debugger. To use Pry in this capacity it must be invoked at runtime in the middle of a running program. This is (as you will see) very easy to do and enables all sorts of interesting possibilities.

<a name="Invoke_on_binding">

### Invoking on a binding

The standard (and recommended) way to invoke Pry at runtime is to use
`binding.pry`. Starting Pry this way ensures that all local variables
and other relevant state (such as [default definee and
cref](http://yugui.jp/articles/846)) are inherited by the session. It
also causes the <a href="#Whereami">`whereami`</a> command to be invoked automatically - and
so the surounding context of the session (a few lines either side of the
invocation line) are displayed for the user.

Note that we can put `binding.pry` _anywhere_ in our program at the
point we want the session to start, and the `self` of the session will
be the self at that point (of course, we can also invoke Pry on _any_ `Binding` object, not just the current one returned by the `binding` method).

Also note that we are not limited to remaining in the scope where the `binding.pry` call was invoked - using Pry's [state navigation](https://github.com/pry/pry/wiki/State-navigation) abilities we can navigate to _any_ part of the program we wish and examine the state there (see [this](http://vimeo.com/23634437) screencast for more information).

When the session ends (by typing `exit` or `exit-all`) the program continues with any modifications you
made to it.

Example:

code:

```ruby
# test.rb
require 'pry'

class A
  def hello() puts "hello world!" end
end

a = A.new

# start a REPL session
binding.pry

# program resumes here (after pry session)
puts "program resumes here."
```

Pry session:

```
pry(main)> a.hello
hello world!
=> nil
pry(main)> def a.goodbye
pry(main)*   puts "goodbye cruel world!"
pry(main)* end
=> nil
pry(main)> a.goodbye
goodbye cruel world!
=> nil
pry(main)> exit

program resumes here.
```

<a href="#Back_to_top">Back to top</a>

<a name="Invoke_on_object">

### Invoking on an object

A Pry session does not have to be invoked on a `Binding`; in fact we
can start a Pry session on any Ruby object, including immediate
values. The syntax for starting a session on an object is `obj.pry`.

When we invoke `pry` on an object a fresh `Binding`
for that session will be generated enabling us to get and set
locals. Note that this `Binding` is just for convenience inside the
session and has no effect on the locals available outside the
session. If you want your changes to locals to persist outside the
session then consider invoking `pry` on a `Binding` object instead.

Example:

code:

```ruby
require 'pry'

5.pry
```

Pry session:

```
pry(5)> self
=> 5
pry(5)> self + 10
=> 15
```

<a href="#Back_to_top">Back to top</a>

<a name="Customize_session">

### Customizing your runtime session

As discussed in the [Customization and
configuration](https://github.com/pry/pry/wiki/Customization-and-configuration)
section, Pry can be customized to suit your needs.  Some common
customizations for runtime invocation include turning off `.pryrc`
loading,  and turning off history before invoking `binding.pry`

Example:

```ruby
Pry.config.should_load_rc = false
Pry.config.history.should_save = false
Pry.config.history.should_load = false

binding.pry
```

We can also perform deeper customizations, such as using a different
[command set](https://github.com/pry/pry/wiki/Command-system#Commandset), or changing the prompt. To perform these customizations we
need to invoke Pry slightly differently so that we can pass these
parameters in:

Example:

code:

```ruby
command_set = Pry::CommandSet.new do
  command "greet" do |name|
    output.puts "hello #{name}"
  end
end

Pry.start binding, :commands => command_set
```

Pry session:

```
pry(main)> help

Command List:
--
install           Install a disabled command.
help              This menu.
greet             No description.
pry(main)> greet John
hello John
```

<a href="#Back_to_top">Back to top</a>

<a name="Whereami">

### whereami command

When a runtime session is invoked on a `Binding` the `whereami`
command is automatically executed (as discussed above). However, the command
can also be invoked explicitly by typing `whereami` in the
REPL.

You can also pass a single numeric parameter to `whereami`, this
number indicates how much context to show. Passing in the parameter
"10" will display 10 lines before and 10 lines after the invocation
line, for example.

Example:

```
pry(#<Bacon::Context:0x101136508>)> whereami 5

From: test/test_pry.rb @ line 95 in Bacon::Context#N/A:

    90:       it 'should suppress output if input ends in a ";" and is an Exception object (single line)' do
    91:         o = Object.new
    92:         str_output = StringIO.new
    93:
    94:         pry_tester = Pry.new(:input => InputTester.new("Exception.new;"), :output => str_output)
 => 95:         binding.pry
    96:         pry_tester.rep(o)
    97:         str_output.string.should == ""
    98:       end
    99:
   100:       it 'should suppress output if input ends in a ";" (single line)' do
pry(#<Bacon::Context:0x101136508>)>
```

<a href="#Back_to_top">Back to top</a>

<a name="Pry_backtrace">

### The `pry-backtrace` command

Show the backtrace for the position in the code where Pry was started. This can be used to
infer the behavior of the program immediately before it entered Pry, just like the backtrace
property of an exception.

```
crow:play john$ ruby gamma.rb 

From: gamma.rb @ line 12 in Object#gamma:

     7: def beta
     8:   gamma("hello")
     9: end
    10: 
    11: def gamma(x)
 => 12:   binding.pry
    13: end  
    14: 
    15: alpha

[1] (pry) main: 0> pry-backtrace

Backtrace:
--
gamma.rb:12:in `gamma'
gamma.rb:8:in `beta'
gamma.rb:4:in `alpha'
gamma.rb:15:in `<main>'
[2] (pry) main: 0> 
```
<a href="#Back_to_top">Back to top</a>

<a name="Runtime_cases">

### Runtime use cases

There are a variety of use cases for runtime invocation of a Pry
session, some include:

**Developer Console:**

Having Pry run in its own thread giving you interactive access to your
application while it is running.

**Debugging:**

Inserting a `binding.pry` at the point in your program you want to
debug, making all state at that point available for interactive inspection (including locals).

**REPL-oriented development and hot-patching:**

Modifying the program while it is running to provide new
features.

<a href="#Back_to_top">Back to top</a>