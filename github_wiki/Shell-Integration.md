# Shell Integration

<a name="Back_to_top">

### Quick Menu:

* <a href="#Execute_shell_commands">Execute arbitrary shell commands</a>
* <a href="#Shell_mode">Shell mode</a>
* <a href="#Cat">The `cat` command</a>
* <a href="#File_dir_locals">The \_file\_ and \_dir\_ locals</a>
* <a href="#Gem_cd">The `gem-cd` command</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

It can be convenient sometimes to access a system shell from within a REPL session. Pry allows arbitrary shell commands to be executed inside the REPL as well as providing a few custom shell commands of its own.

<a name="Execute_shell_commands">

### Execute arbitrary shell commands

In Pry a line of input that begins with a '.' will be forwarded to the command shell. This enables us to navigate the file system, spawn editors, and run git and rake directly from within Pry. 

Note that Ruby code can also be [interpolated](https://github.com/pry/pry/wiki/Command-system#wiki-Command_interpolation) into the shell command using the `#{}` interpolation syntax - see the [command system](https://github.com/pry/pry/wiki/Command-system#wiki-Command_interpolation) section and the example given below, for more information.

Example:

```
pry(main)> .ls -l
total 104
-rw-r--r--   1 john  staff   5109 21 Jun 03:16 CHANGELOG
-rw-r--r--   1 john  staff   1106 21 Apr 19:52 LICENSE
-rw-r--r--   1 john  staff  20538 18 Jun 06:13 README.markdown
-rw-r--r--   1 john  staff   2248 18 Jun 06:13 Rakefile
drwxr-xr-x   4 john  staff    136 21 Jun 03:18 lib
pry(main)> .git log -1 --stat
commit 5c4feac5005b7f74451fd860fa4002f7a2d316e1
Author: John Mair <jrmair@gmail.com>
Date:   Tue Jun 21 03:17:04 2011 +1200

    version 0.9.2 release, updated CHANGELOG and version

 CHANGELOG          |    5 +++++
 lib/pry/version.rb |    2 +-
 2 files changed, 6 insertions(+), 1 deletions(-)
pry(main)>
```

Example: Ruby code can also be interpolated into shell commands using the normal `#{}` string interpolation syntax.

```
pry(main)> x = "[mM]"
=> "[mM]"
pry(main)> .ls | grep #{x}
Documents
Movies
Music
VirtualBox VMs
pry(main)>
```

<a href="#Back_to_top">Back to the top</a>

<a name="Shell_mode">

### Shell mode

As a further convenience Pry features a `shell-mode` command to incorporate the present working directory into the Pry prompt and bring in (limited at this stage, sorry) file name completion. `shell-mode` makes it easier to make effective use of shell commands. Entering `shell-mode` a second time returns you to the ordinary Pry prompt.

Example:

```
pry(main)> shell-mode
pry main:/Users/john/ruby/projects/pry $ .cd ..
pry main:/Users/john/ruby/projects $ .cd ~
pry main:/Users/john $ .pwd
/Users/john
pry main:/Users/john $ shell-mode
pry(main)>
```

<a href="#Back_to_top">Back to the top</a>

<a name="Cat">

### The cat command

The standard system `cat` command is accessible via `.cat` (note the preceding '.' indicating a shell command) however Pry also features its own `cat` command invoked simply by `cat` (no `.` prefix).

Pry's built-in `cat` command features automatic syntax highlighting for a number of file types, including: `.rb, .py, .cpp, .java, .json, .php, .xml` and more. Further, a pager is automatically used when the output spans more than one page.

Pry's `cat` command supports a number of options:

* The `-l` option causes line numbers to be displayed along side each line.
* The `-t [FILE_TYPE]` forces the file to be syntax highlighted as the specified file type. Accepted types include: `ruby`, `cpp`, `python`, `c`, `html`, `javascript`, `diff`, among others.
* The `-s [START_LINE]` specifies the line of the file with which to begin the `cat`. Likewise the `-e [END_LINE]` specifies the line of the file to end the `cat`. Note that negative numbers are accepted (as in most Pry commands); and follow the typical Ruby Array approach where `-1` indicates the last line, and `-2` indicates the second-to-last.
* The `--ex`  option displays a window of lines either side of the line that generated the last exception.
* The `-i` option displays one or more entries from Pry's input expression history. Defaults to showing last 5 input expressions, but can show a range of expressions. To see all expressions use `cat -i 0..-1`. Note that the input expression history only includes pure Ruby code, commands are excluded (they appear as empty strings).

Some use cases for `cat` are for code browsing (facilitated by syntax highlighting, the pager, and line numbers), `cat` is also very useful when combined with the `play` command where lines are extracted from files to replay inside the REPL.

For more info on the `cat --ex` functionality see the [Exception handling](https://github.com/pry/pry/wiki/Exceptions#wiki-cat___ex) section.

Example: `cat` + `play`

```
pry(main)> cat pry_instance.rb -l -s 470
470:     #   valid_expression?("class Hello") #=> false
471:     #   valid_expression?("class Hello; end") #=> true
472:     def valid_expression?(code)
473:       RubyParser.new.parse(code)
474:       true
475:     rescue Racc::ParseError, SyntaxError
476:       false
477:     end
478:   end
479: end
pry(main)> play -f pry_instance.rb --lines 472..477
=> nil
pry(main)> show-method valid_expression?

From: (pry) @ line 72:
Number of lines: 6

def valid_expression?(code)
  RubyParser.new.parse(code)
  true
rescue Racc::ParseError, SyntaxError
  false
end
```

Example: cat --ex (note the line that caused the exception has an arrow pointing to it)

```
pry(main)> hello
invisible green ideas slept furiously
potato
NameError: undefined local variable or method `error' for main:Object
from /Users/john/blah.rb:7:in `hello'
pry(main)> cat --ex

Exception: NameError: undefined local variable or method `error' for main:Object
--
From: /Users/john/blah.rb @ line 7

      1:   def hello
      2:     puts "invisible green ideas slept furiously"
      3:     puts "potato"
      4:     99
=>    5:     this line will generate an error
      6:   end
      7:
      8:   o = 10
```

<a href="#Back_to_top">Back to the top</a>

<a name="File_dir_locals">

### The \_file\_ and \_dir\_ locals

Some commands such as `show-method`, `show-doc`, `show-source`, `stat` and `cat` update the `_file_` and `_dir_` local variables after they run. These locals are especially useful when interpolated into shell commands. 

For more information see the [Special locals](https://github.com/pry/pry/wiki/Special-Locals#wiki-Last_file_and_dir) section.

<a name="Gem_cd">

### The `gem-cd` command

Change the current working directory to the directory of a given gem. Useful when used in conjunction with <a href="#Execute_shell_commands">shell commands</a>.

Example:

```
[1] (pry) main: 0> .pwd
/Users/john/ruby/projects/pry-exception_explorer
[2] (pry) main: 0> gem-cd slop
[3] (pry) main: 0> .pwd
/Users/john/.rvm/gems/ruby-1.9.3-p125/gems/slop-2.4.4
[4] (pry) main: 0> 
```

<a href="#Back_to_top">Back to the top</a>