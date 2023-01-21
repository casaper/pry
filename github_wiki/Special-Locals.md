# The Special Locals

<a name="Back_to_top">

### Quick Menu:

* <a href="#Last_result">Last result</a>
* <a href="#Last_exception">Last exception</a>
* <a href="#In_and_out">The input and output cache</a>
* <a href="#Last_file_and_dir">Last file, and last directory</a>
* <a href="#Pry_local">Current Pry instance</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

Pry injects a number of local variables into a session. Among them is the familiar 'last result' local -  `_` from IRB. 

The role of a special local is to streamline the user interface by making useful information readily available to the programmer.

<a name="Last_result">

### Last result 

**local name**: `_`

The `_` local always stores the result of the last expression, allowing you to recall it for use in new calculations.

Example:

```
pry(main)> 5 + 5
=> 10
pry(main)> _ + 10
=> 20
```

<a href="#Back_to_top">Back to top</a>

<a name="Last_exception">

### Last exception 

**local name**: `_ex_`

Pry gives you access to the most recently caught exception in a local variable called `_ex_`. Unlike Ruby's builtin `$!`, this persists until another exception is raised, so can be used for detailed digging.

You can also use the the `wtf?` command to view the exception's backtrace, and `cat --ex` to view the code that raised the exception. For more information on exceptions check out the [Exception handling](https://github.com/pry/pry/wiki/Exceptions) section.

Example:

```
pry(main)> 4 + "j"
TypeError: String can't be coerced into Fixnum
from (pry):2:in `+'
pry(main)> _ex_
=> #<TypeError: String can't be coerced into Fixnum>
pry(main)> _ex_.backtrace.first(4)
=> ["(pry):2:in `+'",
 "(pry):2:in `<main>'",
 "/Users/john/ruby/projects/pry/lib/pry/pry_instance.rb:229:in `eval'",
 "/Users/john/ruby/projects/pry/lib/pry/pry_instance.rb:229:in `re'"]
pry(main)> 
```

<a href="#Back_to_top">Back to top</a>

<a name="In_and_out">

### The input and output cache

**local names**: `_in_` and `_out_`

Input expressions and output results are automatically stored in array-like data structures accessible from the `_in_` and `_out_` locals respectively. These lists only contain pure Ruby code and
so _exclude_ Pry commands and their results. Also note that the `_in_`
cache stores full *expressions* not just lines of code. The size of the `_in_` and `_out_` cache is determined by `Pry.config.memory_size`, when this limit is reached newer entries begin to overwrite the older ones.

Note that in the examples below the input/output cache index is displayed on the far left; some prompts have this by default, such as the `Pry::NAV_PROMPT`

If we want to  replay an expression from the `_in_`
cache it is best to use the <a href="#Play">play</a>  command. We use it like this: `play --in 5`. 

Example: The `_out_` cache. 

```
pry(main)> 1 + 1
=> 2
pry(main)> 2 + 3
=> 5
pry(main)> _out_[-1] + _out_[-2]
=> 7
```

Example: The `_in_` cache.

```
[5] pry(main)> def poem
[5] pry(main)*   puts "come back to me brutal, empty room"
[5] pry(main)*   puts "thin byzantine face"
[5] pry(main)* end;  
[6] pry(main)> play --in 5 --lines 2..3
come back to me brutal, empty room
thin byzantine face
=> nil
```

<a href="#Back_to_top">Back to top</a>

<a name="Last_file_and_dir">

### Last file, and last directory 

**local names**: `_file_` and `_dir_`

Some commands such as `show-method`, `show-doc`, `show-source`, `stat` and `cat` update the `_file_` and `_dir_` local variables after they run. These locals contain the full path to the file involved in the last command as well as the directory containing that file.

You can then use these special locals in conjunction with Pry commands to do such things as change directory into the directory containing the file, open the file in an editor, display the file using [`cat`](https://github.com/pry/pry/wiki/Shell-Integration#wiki-Cat), and so on.  `__LINE__` may also come in handy here, if you need to access the current line number.

Example:

```
pry(main)> show-method pry

From: /Users/john/ruby/projects/pry/lib/pry/core_extensions.rb @ line 19:
Number of lines: 3

def pry(target=self)
  Pry.start(target)
end
pry(main)> _file_
=> "/Users/john/ruby/projects/pry/lib/pry/core_extensions.rb"
pry(main)> cat #{_file_} -e 2
class Object
  # Start a Pry REPL.
pry(main)> shell-mode
pry main:/ $ .cd #{_dir_}
pry main:/Users/john/ruby/projects/pry/lib/pry $
```

<a href="#Back_to_top">Back to top</a>

<a name="Pry_local">

### Current Pry instance 

**local name**: `pry_instance`

The `pry_instance` local stores a reference to the `Pry` instance managing the current session. By interacting with this instance we can introspect on the machinery behind the REPL itself and further configure/customize our session at runtime. See the [Customization and configuration](https://github.com/pry/pry/wiki/Customization-and-configuration#wiki-Config_runtime) section for more information.

Example:

```
pry(main)> pry_instance.prompt = proc { "> " };
> puts "look at my new prompt!"
look at my new prompt!
=> nil
> 
```

Prior to [v0.13.0](https://github.com/pry/pry/blob/master/CHANGELOG.md#v0130-march-21-2020) this local was named `_pry_`.

<a href="#Back_to_top">Back to top</a>