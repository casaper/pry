# Hooks API

<a name="Back_to_top"></a>
### Quick Menu:

* <a href="#Hooks_built_in_events">Built-in Events</a>
  * <a href="#Hooks_life_cycle_events">Life-cycle Events</a>
    * <a href="#Hooks_when_started_event">:when_started Event</a>
  * <a href="#Hooks_repl_events">REPL Events</a>
    * <a href="#Hooks_before_session_event">:before_session Event</a>
    * <a href="#Hooks_after_read_event">:after_read Event</a>
    * <a href="#Hooks_before_eval_event">:before_eval Event</a>
    * <a href="#Hooks_after_eval_event">:after_eval Event</a>
    * <a href="#Hooks_after_session_event">:after_session Event</a>
  * <a href="#Hooks_index_of_built_in_events">Index of Built-in Events</a>
* <a href="#Hooks_registering_hooks">Registering Hooks</a>
* <a href="#Hooks_adding_custom_events">Adding Custom Events</a>


[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

As another means of integrating with Pry, Pry offers a number of built-in events
that can be used to hook into Pry and register behavior after each Pry instance
is initialized or at various points in the Pry read-eval-print loop (REPL).

Going beyond Pry's built-in event hooks, the hooks API also allows plugins to
register and execute their own events that other plugins can integrate with.

<a name="Hooks_built_in_events"></a>
### Built-in Events

The built-in events that make up Pry's hooks API fall into two categories,
[life-cycle events](#Hooks_life_cycle_events) and [REPL
events](#Hooks_repl_events).

<a name="Hooks_life_cycle_events"></a>
#### Life-cycle Events

There is presently only one life-cycle event:
[`:when_started`](#Hooks_when_started_event).

<a name="Hooks_when_started_event"></a>
##### The `:when_started` Event

The `:when_started` event allows arbitrary code to be executed whenever a new Pry
instance is initialized. In a sense, the `:when_started` event can be thought of
as a post-initialization hook allowing plugins to extend the `Pry#initialize`
method with additional logic and behavior.

The arguments given to `:when_started` hooks are: the target of the new Pry
instance (E.g. the `binding` in `binding.pry`); the Hash of options given to the
Pry instance at initialization; and finally, the new Pry instance.

`:when_started` should be used by plugins that are interested in the original
target object, plugins that are interested in the options given to the Pry
instance or, plugins that wish to take action on a Pry instance immediately
after initialization.

<a href="#Back_to_top">Back to top</a>

<a name="Hooks_repl_events"></a>
#### REPL Events

As one might expect of a REPL, the majority of Pry's events hook into Pry's
read-eval-print loop. Pry has five REPL events. In order of when they tend to
occur they are: [`:before_session`](#Hooks_before_session_event),
[`:after_read`](#Hooks_after_read_event),
[`:before_eval`](#Hooks_before_eval_event),
[`:after_eval`](#Hooks_after_eval_event), and
[`:after_session`](#Hooks_after_session_event).

<a name="Hooks_before_session_event"></a>
##### The `:before_session` Event

The `:before_session` event fires after each REPL session starts.

The arguments given to `:before_session` hooks are: The output object, the
current binding, and the Pry instance.

<a name="Hooks_after_read_event"></a>
##### The `:after_read` Event

The `:after_read` event fires after each line of input is read.

The arguments given to `:after_read` hooks are: The input string and the Pry
instance.

<a name="Hooks_before_eval_event"></a>
##### The `:before_eval` Event

The `:before_eval` event fires before each input statement is evaluated.

The arguments given to `:before_eval` hooks are: The code to be evaluated and
the Pry instance.

<a name="Hooks_after_eval_event"></a>
##### The `:after_eval` Event

The `:after_eval` event fires after each input statement is evaluated.

The arguments given to `:after_eval` hooks are: The result of the evaluation
and the Pry instance.

<a name="Hooks_after_session_event"></a>
##### The `:after_session` Event

The `:after_session` event fires after each REPL session.

The arguments given to `:after_session` hooks are: The output object, the
current binding, and the Pry instance.

<a href="#Back_to_top">Back to top</a>

<a name="Hooks_index_of_built_in_events"></a>
#### Index of Built-in Events

Event                                           | Family     | When invoked                             | Arguments
----------------------------------------------- | ---------- | ---------------------------------------- | -----------
[`:when_started`](#Hooks_when_started_event)     | life-cycle | After `Pry#initialize`                   | The target object, the options Hash, and the new Pry instance
[`:before_session`](#Hooks_before_session_event) | REPL       | Before each REPL session starts          | The output object, the current binding, and the Pry instance
[`:after_read`](#Hooks_after_read_event)         | REPL       | After each line of input is read         | The input String and the Pry instance
[`:before_eval`](#Hooks_before_eval_event)       | REPL       | Before each input statement is evaluated | The code to be evaluated and the Pry instance
[`:after_eval`](#Hooks_after_eval_event)         | REPL       | After each input statement is evaluated  | The result of the evaluation and the Pry instance
[`:after_session`](#Hooks_after_session_event)   | REPL       | After each REPL session                  | The output object, the current binding, and the Pry instance

<a href="#Back_to_top">Back to top</a>

<a name="Hooks_registering_hooks"></a>
### Registering Hooks

New hooks can be registered for any of Pry's events using the
`add_hook` method of the `Pry.hooks` object (an instance of
`Pry::Hooks`). The `add_hook` method takes two arguments and a block. The first
argument is the name of the event to add a hook for and the second argument
is a name for the hook so that it may be referenced later. For example, a
`:before_session` hook could be registered like so:

```ruby
Pry.hooks.add_hook(:before_session, "my_hook") do |output, binding, pry|
  output.puts("Hello, World!")
end
```

Here are a few examples of when the above `:before_session` hook would fire
when run in a vanilla Pry session:

```
$ pry
Hello, World!
[1] pry(main)> Pry.start
Hello, World!
[1] pry(main)> exit
[2] pry(main)> binding.pry

# ... Skip output of `whereami` command ...

Hello, World!
[1] pry(#<Pry>)> exit
[3] pry(main)>
```

<a href="#Back_to_top">Back to top</a>

<a name="Hooks_adding_custom_events"></a>
### Adding Custom Events

Adding custom events that other Pry plugins can add hooks for is surprisingly
simple. Though one might expect to have to register a new event, as it works
out, Pry always assumes an event exists when adding hooks because it can't
always be expected that an event will be defined before hooks are added for that
event. This assumed existence means that there's no need to register a new event
or anything like that, all that you must do is execute the event with whatever
arguments you deem appropriate.

Custom events can be executed using the `Pry.hooks.exec_hook` method. For
example, consider the following snippet which adds a `:before_sleep` event that
is fired when `SIGTSTP` is received and adds a hook to puts "Good
night!" whenever that event is fired:

```ruby
# Add a Pry event before the TSTP signal backgrounds the process
handler, old_handler = nil
handler = proc do |sig_value|
  Pry.hooks.exec_hook(:before_sleep)
  # There's probably a better way to invoke the default signal handler.
  trap("TSTP", old_handler)
  Process.kill("TSTP", 0)
  old_handler = trap("TSTP", &handler)
end
old_handler = trap("TSTP", &handler)

# Now register a hook
Pry.hooks.add_hook(:before_sleep, :say_good_night_before_sleep) do
  puts "\nGood night!"
end
```

<a href="#Back_to_top">Back to top</a>