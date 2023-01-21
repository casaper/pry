# Documentation browsing

![show-doc](http://img-fotki.yandex.ru/get/5624/98991937.10/0_8ed2e_eac95b26_orig)

<a name="Back_to_top">

### Quick Menu:

* <a href="#Show_doc">View method and class documentation</a>
* <a href="#Stat">View basic method information</a>
* <a href="#Ri">Access ri documentation</a>
* <a href="#C_doc">View documentation for Ruby Core (C code)</a>

<a href="https://github.com/pry/pry/wiki"><img src="http://img-fotki.yandex.ru/get/4131/98991937.10/0_8ed1e_b36c4722_orig" /></a>
[Home page](https://github.com/pry/pry/wiki)

### Overview

The ability to retrieve method documentation is very important when learning a new library or code base. And the ability to read documentation in a REPL environment, where you can interact with the methods on live code is particularly useful. Pry does this without relying on any external utilities, it simply extracts what it needs at run-time from the source file. This makes Pry's native documentation viewer potentially more useful than tools such as `ri` as Pry does not rely on docs being pre-generated when a gem is installed.

<a name="Show_doc">

### View method and class documentation

To retrieve a method or class's documentation use the `show-doc` command. `show-doc` accepts the same options as the [`show-source`](https://github.com/pry/pry/wiki/Source-browsing) command and, like `show-source` extracts what it needs at run-time.

`show-doc` has a basic understanding of both the `rdoc` and `YARD` documentation formats and will attempt to render the documentation properly and with syntax highlighting. 

Note that, as in the case of [`show-source`](https://github.com/pry/pry/wiki/Source-browsing#wiki-Current_method_source), you can simply type `show-doc` (with no arguments) to operate on the 'current method' (when Pry is invoked inside a method using `binding.pry` at [runtime](https://github.com/pry/pry/wiki/Runtime-invocation)).

Note: show-doc's 'number of lines' shows the total lines of documentation.

The following options are supported:

* Use the `-s` option to select the super method. You can repeat `-ss` to <a href="#super-flag">get to the super method's super method</a>.
* Use the `-l` option to include line numbers
* Use the `-b` option to include line numbers, but start numbering at 1 (useful when used with `play`)
* Use the `-a` option to show comments for all definitions and monkey-patches of the module/class

Aliases: `?`

Example ([recorded](http://showterm.io/95a1fd3d1b4cf9caa6875)): viewing documentation for a class

```
[1] pry(main)> show-doc Pry::Method

From: /home/curacao/.gem/ruby/1.9.3/gems/pry-0.9.11.4/lib/pry/method.rb 
Number of lines: 2

This class wraps the normal `Method` and `UnboundMethod` classes
to provide extra functionality useful to Pry.
[2] pry(main)>
```

Example ([recorded](http://showterm.io/231a11adf48b80e9dc415)): with `ri` syntax

```
[1] pry(main)> require 'set'
=> true
[2] pry(main)> show-doc Set#add

From: /opt/rubies/ruby-1.9.3-p327/lib/ruby/1.9.1/set.rb @ line 226:
Owner: Set
Visibility: public
Signature: add(o)
Number of lines: 2

Adds the given object to the set and returns self.  Use merge to
add many elements at once.
[3] pry(main)>
```

Example ([recorded](http://showterm.io/275b40e1b9ce1114bf75d)): YARD

```
[1] pry(main)> show-doc Pry#r

From: /home/curacao/.gem/ruby/1.9.3/gems/pry-0.9.11.4/lib/pry/pry_instance.rb @ line 284:
Owner: Pry
Visibility: public
Signature: r(target=?, eval_string=?)
Number of lines: 10

Perform a read.
If no parameter is given, default to top-level (main).
This is a multi-line read; so the read continues until a valid
Ruby expression is received.
Pry commands are also accepted here and operate on the target.
param [Object, Binding] target The receiver of the read.
param [String] eval_string Optionally Prime `eval_string` with a start value.
return [String] The Ruby expression.
example
  Pry.new.r(Object.new)
```

<a href="#Back_to_top">![Back][top] Back</a>

Example ([recorded](http://showterm.io/f488093a1d79243dcb517)): superclass methods documentation (with help of `-s` or `--super` flag).

```
[1] pry(main)> class Parent
[1] pry(main)*   # Parent!
[1] pry(main)*   def foo; end
[1] pry(main)* end  
=> nil
[2] pry(main)> class Child < Parent
[2] pry(main)*   # Child!
[2] pry(main)*   def foo; end
[2] pry(main)* end  
=> nil
[3] pry(main)> show-doc Parent#foo

From: (pry) @ line 2:
Owner: Parent
Visibility: public
Signature: foo()
Number of lines: 1

Parent!
[4] pry(main)> show-doc Child#foo

From: (pry) @ line 6:
Owner: Child
Visibility: public
Signature: foo()
Number of lines: 1

Child!
[5] pry(main)> show-doc Child#foo -s

From: (pry) @ line 2:
Owner: Parent
Visibility: public
Signature: foo()
Number of lines: 1

Parent!
[6] pry(main)> class ChildChild < Child
[6] pry(main)*   # ChildChild!
[6] pry(main)*   def foo; end
[6] pry(main)* end  
=> nil
[7] pry(main)> show-doc ChildChild#foo

From: (pry) @ line 10:
Owner: ChildChild
Visibility: public
Signature: foo()
Number of lines: 1

ChildChild!
[8] pry(main)> show-doc ChildChild#foo -s

From: (pry) @ line 6:
Owner: Child
Visibility: public
Signature: foo()
Number of lines: 1

Child!
[9] pry(main)> show-doc ChildChild#foo -ss

From: (pry) @ line 2:
Owner: Parent
Visibility: public
Signature: foo()
Number of lines: 1

Parent!
[10] pry(main)>
```

<a href="#Back_to_top">![Back][top] Back</a>

Example ([recorded](http://showterm.io/cf13164cbdfa01a8a5298)): view the documentation of all definitions and monkey-patches of classes or modules. Note that Pry assumes a class is only monkey-patched at most _once_ per file, so will not find multiple monkey-patches in the _same_ file.

```
[1] pry(main)> edit a.rb
[2] pry(main)> edit b.rb
[3] pry(main)> show-doc A

From: /home/curacao/a.rb 
Number of lines: 1

Original.
[4] pry(main)> show-doc A -a
Found 2 candidates for `A` definition:

Candidate 1/2: /home/curacao/a.rb @ line 2:
Number of lines: 1

Original.

Candidate 2/2: /home/curacao/b.rb @ line 2:
Number of lines: 1

Reopened.
[5] pry(main)>
```

<a href="#Back_to_top">![Back][top] Back</a>

<a name="Stat">

### View basic method information

The `stat` command displays basic information about a method, including: its arity, its owner, its signature (if possible), and its type (i.e whether it's bound or unbound).

Example ([recorded](http://showterm.io/b8397f7d3f1b71e63a386)):

```
[1] pry(main)> stat Array#length
Method Information:
--
Name: length
Alias: size
Owner: Array
Visibility: public
Type: Unbound
Arity: 0
Method Signature: length()
Source Location: Not found.
[2] pry(main)>
```

<a href="#Back_to_top">![Back][top] Back</a>

<a name="Ri">

### Access ri documentation

The standard `ri` shell command can be accessed from within a Pry session using exactly the same syntax it has on the command line.

Example ([recorded](http://showterm.io/c82d879221859826b1c11)):

```
[1] pry(main)> ri Fixnum#++
Fixnum#+

(from ruby core)
------------------------------------------------------------------------------
  fix + numeric  ->  numeric_result
   

------------------------------------------------------------------------------

Performs addition: the class of the resulting object depends on the class of
numeric and on the magnitude of the result.


[2] pry(main)> 
```

<a href="#Back_to_top">![Back][top] Back</a>

<a name="C_doc">

### View documentation for Ruby Core (C code)

When the <a href="https://github.com/pry/pry-doc">![][external_link] pry-doc</a> plugin is installed (`gem install pry-doc`) the C documentation for Ruby core methods (MRI) become available. As with pure Ruby methods, the `show-doc` command is used to display the documentation.

Example ([recorded](http://showterm.io/22b3e39ad406e66e5ef00)):

```
[1] pry(main)> show-doc Object#extend

From: eval.c (C Method):
Owner: Kernel
Visibility: public
Signature: extend(*arg1)
Number of lines: 19

Adds to _obj_ the instance methods from each module given as a
parameter.

   module Mod
     def hello
       "Hello from Mod.\n"
     end
   end

   class Klass
     def hello
       "Hello from Klass.\n"
     end
   end

   k = Klass.new
   k.hello         #=> "Hello from Klass.\n"
   k.extend(Mod)   #=> #<Klass:0x401b3bc8>
   k.hello         #=> "Hello from Mod.\n"
[2] pry(main)>
```

<a href="#Back_to_top">![Back][top] Back</a>

[external_link]: http://img-fotki.yandex.ru/get/6443/98991937.10/0_8ed28_b5bf519f_orig
[top]: http://img-fotki.yandex.ru/get/5638/98991937.10/0_8ed2c_2c7b9e70_orig