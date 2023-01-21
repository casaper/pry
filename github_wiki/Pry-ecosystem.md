## Quick Menu:

* [Debugging](#debugging)
* [Remote Sessions](#remote)
* [Error Consoles](#consoles)
* [What's Next for Pry?](#next)
* [We Need Your Help](#help)

Roughly a year ago, I wrote my [first article][first article] introducing Pry to
the Ruby world. Back then Pry was a very different creature, just a simple
runtime variant of IRB with a few added extras. In the year that's passed, Pry
has grown from a single project into an ecosystem, with a variety of plugins and
spinoffs. In this post I'll describe the current state of the Pry ecosystem,
provide a brief synopsis of some of the plugins available, and attempt to chart
the course of future development.

<a name="debugging"></a>
## Debugging

Aside from its use as an IRB alternative, Pry's other primary use case is as a
debugger. The Pry gem by itself hasn't any facilities for a ruby-debug style
`next` or `step`, so any debugging was limited to examining program snapshots
around the `binding.pry` call.

Now, however, two recent plugins, pry-nav and pry-stack_explorer, have
transformed Pry into a full-featured debugger.

### [pry-nav][pry-nav]

This plugin adds the `next`, `step`, and `continue` commands to Pry. After
requiring this gem, you can use Pry as normal, placing `binding.pry`
wherever you want to start your session. The `next` command will advance execution
by one line:

```ruby
[1] (pry) main: 0> next

From: ./y.rb @ line 5 in Object#hello:

    1: require 'pry-nav'
    2:
    3: def hello
    4:   binding.pry
 => 5:   x = 20
    6: end
    7:
    8: hello
```

pry-nav also has support for [pry-remote][pry-remote], enabling you to perform
remote debugging.

### [pry-stack_explorer][pry-stack_explorer]

This plugin implements the other side of the debugging coin, call-stack
navigation. It provides the [show-stack][show-stack], [frame][frame], [up][up]
and [down][down] commands. From the point a Pry session is started, you can move
up the call stack through parent stack frames, examine state, and even evaluate
code!

```ruby
[2] (pry) main: 0> show-stack

Showing all accessible frames in stack (5 in total):
--
=> #0 [method] gamma
   #1 [method] beta
   #2 [method] alpha
```

While these two gems create a powerful debugger, you may at some point find
yourself needing to use [ruby-debug][ruby-debug].  If so, you can embed Pry into
it with the [ruby-debug-pry][ruby-debug-pry] gem.

<a name="remote"></a>
## Remote sessions

Remote sessions have been another important development in the Pry ecosystem. A
remote session allows you to start instances of Pry in a running program and
connect to those instances over a socket. This is particularly useful for using
Pry in places not normally possible, such as apps running on the [Pow][pow] web
server. It also opens the door for such exotica as multi-user sessions.

Pry has two plugins that implement remote sessions: `pry-remote` and the newer
and more ambitious `pry-remote-em`.

### [pry-remote][pry-remote]

pry-remote uses the [DRb][drb] and should work on Ruby versions and
implementations. To use it, simply replace the normal `binding.pry` call with
`binding.remote_pry` as the example below illustrates. A DRb server will await
connections, and a remote session will start as soon as a client connects.

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

We can then connect to it using the `pry-remote` executable.

### [pry-remote-em][pry-remote-em]

pry-remote-em is a sophisticated EventMachine-based alternative to
pry-remote. It adds user authentication and SSL support along with tab
completion and paging. It also allows multiple clients to connect to the same
server, and multiple servers to run on the same computer and even within the
same process.

I'm very excited about `pry-remote-em`, as it opens up possibilities for
multi-user remote debugging and exploration as well as educational applications.
It is also just fun to interact with other programmers in a live environment;
for example, a Ruby variant of [core war][core war] could easily run on the
`pry-remote-em` base.

To start the pry-remote-em server:

```ruby
require 'pry-remote-em/server'

class Foo
  def initialize(x, y)
    binding.remote_pry_em
  end
end

EM.run { Foo.new 10, 20 }
```

And, similar to pry-remote, we  connect using the `pry-remote-em` executable.

<a name="consoles"></a>
## Error consoles

As discussed in Avdi Grimm's blog post [Hammertime: An interactive error console
for Ruby][hammer time], the Lisp and Smalltalk communities have great tools for
debugging exceptions. Instead of allowing their programs to die from an
un-rescued exception, their programming environments present them with an
interactive console and give them an opportunity to correct the problem.

Avdi's nifty [Hammertime][hammer time gh] gem is one attempt to bring this
functionality to Ruby. pry-exception_explorer is another. A specialized version
of pry-exception_explorer for intercepting test failures, called Plymouth, is
also available.

### [pry-exception_explorer][pry-exception_explorer]

When your program raises an exception, pry-exception_explorer intercepts it and
starts a Pry session in the context where the exception was raised. The user has
access to all state at the error site. Furthermore, the user can walk up the
exception's entire backtrace, evaluating arbitrary code at each level, to
isolate the precise cause of the error.

Another feature of pry-exception_explorer is the ability to define exactly which
exceptions should be captured. This can be as simple as specifying an exception
class, or as sophisticated as an assertion over the entire state of the call
stack. Rudimentary support for some C-level exceptions is also provided and
activated with a command line switch.

In the example below, we configure a call-stack assertion so that Pry starts
when an ArgumentError is raised, but only if the exception context is an
instance of MyClass and the parent's context is an instance of MyCallingClass:

```ruby
EE.intercept do |frame, ex|
  ex.is_a?(ArgumentError) && frame.klass == MyClass &&
  frame.prev.klass == MyCallingClass
end
```

Pry-exception_explorer has proven itself to be very useful, often obviating the
need for a real debugger. For a quick demo of it in action, check out the
following [mini screencast][mini screencast].

However, this plugin has been deprecated in favor of
<a href="https://github.com/ConradIrwin/pry-rescue">pry-rescue</a>.

### [Plymouth][plymouth]

Plymouth is a specialized error console designed to help debug failing tests.
When a test fails, a Pry session starts immediately at the site of the failure.
You can execute Ruby in the REPL to figure out what's wrong, and you can also
use `edit --current` to fix the failed test in your text editor.

Plymouth illustrates the power of pry-exception_explorer; its functionality is
implemented using the pry-exception_explorer API. In fact, the following code is
all that's required to intercept failing tests on RSpec:

```ruby
EE.intercept do |frame, ex|
  if ex.class.name =~ /RSpec::Expectations::ExpectationNotMetError/
    message = ex.message
    true
  else
    false
  end
end.skip_until do |frame|
  frame.klass.name =~ /RSpec::Core::ExampleGroup::Nested/
end
```
Plymouth currently supports the RSpec, Bacon and MiniTest libraries.  Support
for other libraries is coming soon.

<a name="next"></a>
## What's next for Pry?

The projects described above represent a generous slice of the Pry universe, but
there's still a lot more out there—plugins for [live syntax
highlighting][coolline], [Rails integration][pry-rails], and [bash alias
integration][bash alias], to name just a few. The Pry ecosystem is expanding at
an impressive rate.

The ultimate goal of the Pry project is to make REPL-driven development a
reality for the Ruby language and to get as close to the Smalltalk model as we
can, even to the point of implementing a GUI front-end for Pry.

<a name="help"></a>
## We need your help

Pry has been a huge amount of work over the last year or so, but we've made
good progress. Multi-user Pry sessions, full debugging functionality, and
remote sessions are now a reality.

If you would like to see the Pry project come to fruition, please help us work
through the [issues][issues]. If you're too busy, you might consider making a
[monetary donation][pledgie]. The core team is small and there's only so much we
can do, but with a bit of funding the project can develop at a faster rate and
with a higher degree of polish.  Alternatively, if your company would like to
sponsor the project, you can [contact the project maintainer][big boss] to make
arrangements.

I'd like to thank everyone who's used and contributed to Pry over the last year.
With your help, I hope this next year will be even better.

[first article]: http://banisterfiend.wordpress.com/2011/01/27/turning-irb-on-its-head-with-pry
[pry-nav]: https://github.com/nixme/pry-nav
[pry-remote]: https://github.com/mon-ouie/pry-remote
[pry-stack_explorer]: https://github.com/pry/pry-stack_explorer
[show-stack]: https://gist.github.com/ed0cd3c299ddbd912e6d
[frame]: https://gist.github.com/ed0cd3c299ddbd912e6d
[up]: https://gist.github.com/56275912f7e51a7dbb9a
[down]: https://gist.github.com/56275912f7e51a7dbb9a
[ruby-debug]: https://github.com/mark-moseley/ruby-debug
[ruby-debug-pry]: https://github.com/AndrewO/ruby-debug-pry
[pow]: http://pow.cx/
[drb]: http://www.ruby-doc.org/stdlib-2.1.0/libdoc/drb/rdoc/DRb.html
[pry-remote-em]: https://github.com/simulacre/pry-remote-em
[core-war]: http://en.wikipedia.org/wiki/Core_War
[hammer time]: http://avdi.org/devblog/2010/01/18/hammertime/
[hammer time gh]: https://github.com/avdi/hammertime
[pry-exception_explorer]: https://github.com/pry/pry-exception_explorer
[mini screencast]: http://vimeo.com/36061298
[plymouth]: http://github.com/banister/plymouth
[coolline]: https://github.com/pry/pry-coolline
[pry-rails]: https://github.com/rweng/pry-rails
[bash alias]: https://github.com/envygeeks/pry-vterm_aliases
[issues]: http://github.com/pry/pry/issues
[pledgie]: http://www.pledgie.com/campaigns/15899
[big boss]: https://github.com/banister