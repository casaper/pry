# State navigation

<a name="Back_to_top">

### Quick Menu:

* <a href="#Changing_scope">Changing scope with cd</a>
* <a href="#Ls">Learning about your context with the ls command</a>
* <a href="#Find_method">Rapidly exploring with the find-method command</a>
* <a href="#Nested_contexts">Seeing the nested contexts</a>
* <a href="#Jump_to_context">Jump to another context</a>
* <a href="#Switch_to_context">Switch to another context</a>
* <a href="#Exit_with_value">Exit a context with a value</a>
* <a href="#Exit_all">Exit all nested contexts</a>
* <a href="#Exit_program">Exit the running program</a>
* <a href="#Control_d_handling">`^D` handling</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

Taking inspiration from the unix command shell, Pry provides commands to make navigating around your program's runtime state both familiar and natural.

<a name="Changing_scope">

### Changing scope with cd

The `cd` command is used to move into a new object (or scope) inside a Pry session. When inside the new scope it becomes the `self` for the session and all commands and methods will operate on this new self.

As in unix shells you use `cd ..` to go back to the previous scope, `cd /` to return to the top-level scope for the Pry session (usually `main` but does not have to be) and `cd -` to switch between last two scopes.

The `cd` command also accepts complex syntax like `cd ../@x/Const/@y`
where the `..` represents the context one level further up the
stack. The above example can be translated into English as the
following: "pop the current context off the
stack, look up @x in the prior context and push it onto the stack,
look up Const in the context of @x and push it onto the stack, look up
@y in the context of Const and push it onto the stack".

This extended `cd` syntax is known as "object path syntax".


Example: Basic use

```
pry(main)> self
=> main
pry(main)> cd Pry
pry(Pry):1> self
=> Pry
pry(Pry):1> cd ..
pry(main)>
```

Example: Extended example showing off object path syntax. 

```
pry(Pry::CommandSet::Command):3> nesting
Nesting status:
--
0. main (Pry top level)
1. Pry
2. Pry::CommandSet
3. Pry::CommandSet::Command
pry(Pry::CommandSet::Command):3> ls Pry -c --grep ^C
CLIPPED_PRINT  CommandContext  CommandProcessor  CommandSet  Commands  Config  
pry(Pry::CommandSet::Command):3> cd ../../Config
pry(Pry::Config):2> nesting
Nesting status:
--
0. main (Pry top level)
1. Pry
2. Pry::Config
pry(Pry::Config):2> ls -c Pry::CommandProcessor
Result  
pry(Pry::Config):2> cd ../CommandProcessor/Result
pry(Pry::CommandProcessor::Result):3> nesting
Nesting status:
--
0. main (Pry top level)
1. Pry
2. Pry::CommandProcessor
3. Pry::CommandProcessor::Result
pry(Pry::CommandProcessor::Result):3> 
```

Example: Showing off `cd -` command. 

```
pry(main)> cd 1/2/3
pry(3)> nesting
Nesting status:
--
0. main (Pry top level)
1. 1
2. 2
3. 3
pry(3)> cd -
pry(main)> nesting
Nesting status:
--
0. main (Pry top level)
pry(main)> cd -
pry(3)> nesting
Nesting status:
--
0. main (Pry top level)
1. 1
2. 2
3. 3
```

<a href="#Back_to_top">Back to the top</a>

<a name="Ls">

### Learning about your context with the ls command

**Note:** This command has been significantly updated since Pry version 0.9.7. Some of the options below may work differently if you are using an earlier version; it is recommended you upgrade.

The `ls` command is essentially a unified wrapper to a number of Ruby's introspection mechanisms, including (but not limited to) the following methods: `methods`, `instance_variables`, `constants`, `local_variables`, `instance_methods`, `class_variables` and all the various permutations thereof.

By default typing `ls` shows you the local variables defined in the current context, and any public methods or instance variables defined on the current object.

Usage: 

```
    ls [-m|-M|-p|-pM] [-q|-v] [-c|-i] [Object]
    ls [-g] [-l]

options:

    -m, --methods        Show public methods defined on the Object (default)
    -M, --module         Show methods defined in a Module or Class
    -p, --ppp            Show public, protected (in yellow) and private (in green) methods
    -q, --quiet          Show only methods defined on object.singleton_class and object.class
    -v, --verbose        Show methods and constants on all super-classes (ignores Pry.config.ls.ceiling)
    -g, --globals        Show global variables, including those builtin to Ruby (in cyan)
    -l, --locals         Show locals, including those provided by Pry (in red)
    -c, --constants      Show constants, highlighting classes (in blue), and exceptions (in purple)
    -i, --ivars          Show instance variables (in blue) and class variables (in bright blue)
    -G, --grep           Filter output by regular expression
    -h, --help           Show help
```

Note that the options can be combined and so `ls -Mi` will return the list of instance methods and instance variables for the current context.

You can also explicitly provide the context if you're interested in an object other than the current context, e.g `ls -m obj` will return the list of public methods defined on `obj`.

Example:

```
pry(main)> cd Grit
pry(Grit):1> ls -c --grep R
constants: GitRuby  InvalidGitRepositoryError  Ref  Remote  Repo  VERSION
pry(Grit):1> ls -M Repo --grep ^b
Grit::Repo methods: bare  batch  blame  blame_tree  blob  branches
pry(Grit):1> cd Repo
pry(Grit::Repo):2> ls -c
constants: BATCH_PARSERS  DAEMON_EXPORT_FILE
pry(Grit::Repo):2> ls -m
Object.self methods: yaml_tag
Grit::Repo.self methods: init  init_bare  init_bare_or_open
```

<a href="#Back_to_top">Back to the top</a>

<a name="Find_method">

### Rapidly exploring with the find-method command

The `find-method` command allows you to easily find which method does what. It's very similar to `grep`, but instead of finding files and line-numbers, it finds ruby methods and source code. This is most useful when you're trying to get a feel for how a particular gem or application is structured, as it lets you answer questions such as "which method actually sends this to the client?", and "what's responsible for serialization".

To use it you just pass in the method name you'd like to find.

Example: find who's using yaml?

```
pry(main)> find-method to_yaml
Gem::Specification#to_yaml
```

Optionally you can also pass in a namespace to narrow down the search. This enables you to quickly find structure in large applications: 

Example: what does Pry do with history?

```
pry(main)> find-method history Pry
Pry.history=
Pry.save_history
Pry.load_history
Pry.history
Pry#update_input_history
Pry::Config
Pry::Config#history
Pry::Config#history=
```

The final way to use `find-method` is with the `-c` flag. When this is present, `find-method` will grep the source code of each method. This can be used to help find methods that are calling other methods, or when searching for something you're sure you remember seeing once:

Example: find methods that call `save_history`:

```
pry(main)> find-method -c save_history Pry
Pry.save_history: def self.save_history
Pry
Pry#repl_epilogue:   Pry.save_history if Pry.config.history.should_save && Pry.active_sessions == 0
```

Example: which method had to be paranoid?

```
pry(main)> find-method -c paranoid Pry
Pry
Pry#show_result:   # Being uber-paranoid here, given that this exception arose because we couldn't
```

<a href="#Back_to_top">Back to the top</a>

<a name="Nested_contexts">

### Seeing the nested contexts

The `nesting` command displays the currently open contexts and their nesting level.

Example:

```
pry(Grit::Git::CommandFailed):3> nesting
Nesting status:
--
0. main (Pry top level)
1. Grit
2. Grit::Git
3. Grit::Git::CommandFailed
pry(Grit::Git::CommandFailed):3>
```

<a href="#Back_to_top">Back to the top</a>

<a name="Jump_to_context">

### Jump to another context

Using the `jump-to` command in concert with the `nesting` command we can identify an earlier context to jump back to; all contexts between the current and the target are exited.

Example:

```
pry(Grit::Git::CommandFailed):3> nesting
Nesting status:
--
0. main (Pry top level)
1. Grit
2. Grit::Git
3. Grit::Git::CommandFailed
pry(Grit::Git::CommandFailed):3> jump-to 1
=> Grit::Git
pry(Grit):1> nesting
Nesting status:
--
0. main (Pry top level)
1. Grit
pry(Grit):1>
```

<a href="#Back_to_top">Back to the top</a>

<a name="Switch_to_context">

### Switch to another context

Unlike the `jump-to` command (discussed above) which exits out of
nested contexts, the `switch-to` command does not exit contexts but starts a new `Pry` instance to
manage the session on the selected binding. Using `switch-to` you can
temporarily switch to a Binding further up the context stack, work on it, then
exit out, returning you to your exact position on the original binding
(even if you were mid-expression).

This functionality can be useful when you are writing a method in one
context, but need to quickly look up some information on another
context further up the context stack (and you do not wish to lose your current
work on the method as you would if you had used `jump-to`).

Example:

```
pry(Pry::CommandSet::Command):3> def test_method
pry(Pry::CommandSet::Command):3*   puts "this is a test"
pry(Pry::CommandSet::Command):3* nesting
Nesting status:
--
0. main (Pry top level)
1. Pry
2. Pry::CommandSet
3. Pry::CommandSet::Command
pry(Pry::CommandSet::Command):3* switch-to 2
pry(Pry::CommandSet)> ls -m
allocate  new  superclass  yaml_tag  
pry(Pry::CommandSet)> exit
pry(Pry::CommandSet::Command):3* show-input
1: def test_method
2:   puts "this is a test"
pry(Pry::CommandSet::Command):3*
```

<a href="#Back_to_top">Back to the top</a>



<a name="Exit_with_value">

### Exit a context with a value

Sometimes it can be useful to exit a context with a user-provided value. This is especially useful when Pry is invoked at runtime as the exit value of the Pry session can be used to determine program flow.

Note that using `exit` only goes back one nesting level, it doesn't exit Pry altogether. So `exit` is equivalent to `cd ..` except that it can return a user-provided a value.

Example:

```
pry(Grit):1> exit "hello world"
=> "hello world"
pry(main)> _
=> "hello world"
pry(main)>
```

<a href="#Back_to_top">Back to the top</a>

<a name="Exit_all">

### Exit all nested contexts and return to the calling process

The `exit-all` command is normally used when Pry is invoked at runtime by a running program. Entering `exit-all` will break out of all nested contexts and return to the calling process, the program that invoked Pry will then continue its execution. Note that `exit-all` also accepts an optional value argument and will return with this value (as in the case of `exit`).

Aliases: `!!@`

Example:

```
pry(Pry::CommandProcessor):2> exit-all 5
# Returns to calling process here with the value 5
```

<a href="#Back_to_top">Back to the top</a>

<a name="Exit_program">

### Exit the program completely

The `exit-program` command ends the current program, including the program that invoked the Pry session. This command is equivalent  to running the Ruby `exit` method which raises the `SystemExit` exception; however history is saved before exit.

Aliases: `!!!`

Example:

```
pry(Pry::CommandProcessor):2> !!!
crow:pry john$
```

<a href="#Back_to_top">Back to the top</a>

<a name="Control_d_handling"></a>

### `^D` handling

Pressing `^D` in a REPL loop gives you different behaviour in different cases. In an expression it behaves like `!` command.

Example ([recorded](http://showterm.io/8a792f9a17696d74a0534)):

```
[1] pry(main)> def you_are_beautiful
[1] pry(main)*   :yes
[1] pry(main)*   ^D
[1] pry(main)>
```

At top-level session it behaves like `exit` command.

Example ([recorded](http://showterm.io/da26a0f71a6ce58bbb0f0)):

```
~% pry
[1] pry(main)> 1
=> 1
[2] pry(main)> 2
=> 2
[3] pry(main)> 3
=> 3
[4] pry(main)> ^D
~%
```

In a nested session `^D` is a synonym for `cd ..` call.

Example ([recorded](http://showterm.io/7f468eb96bf8e1a6fd945)):

```
[1] pry(main)> cd "love"
[2] pry("love"):1> nesting
Nesting status:
--
0. main (Pry top level)
1. "love"
[3] pry("love"):1> ^D
[3] pry(main)> nesting
Nesting status:
--
0. main (Pry top level)
[4] pry(main)> 
```

<a href="#Back_to_top">Back to the top</a>