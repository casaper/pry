# User Input

<a name="Back_to_top">

### Quick Menu:

* <a href="#Clear_input">Clear the input buffer</a>
* <a href="#Suppress_output">Suppress evaluation output</a>
* <a href="#Show_input_buffer">Show the contents of the input buffer</a>
* <a href="#Amend_line">Amend lines of input</a>
* <a href="#Edit_input_buffer">Editing the contents of the input buffer with an editor</a>
* <a href="#Replay_history">Replay history</a>
* <a href="#Play">Play files and methods as input</a>
* <a href="#Inp_out_cache">The `_in_` and `_out_` cache</a>

<a href="https://github.com/pry/pry/wiki"><img src="http://img-fotki.yandex.ru/get/4131/98991937.10/0_8ed1e_b36c4722_orig" /></a>
[Home page](https://github.com/pry/pry/wiki)

### Overview

Unlike some REPLs, where a single mistake can force you to reenter the whole expression, or worse still - get stuck in an endless read loop - Pry has a number of features designed to make entering input a less frustrating experience.

<a name="Clear_input">

### Clear the input buffer

Sometimes the parsing process can go wrong while entering a multi-line expression, and the read loop will never terminate. Entering the `!` character on a line by itself will clear the input buffer and break out of the read loop. Doing this will enable normal input to resume on the next line. The contents of the input buffer will be lost, however.

Example:

```
pry(main)> def hello
pry(main)*   puts "hello"
pry(main)* !
Input buffer cleared!
pry(main)>
```

<a href="#Back_to_top">![Back][top] Back</a>

<a name="Suppress_output">

### Suppress evaluation output

Put a `;` at the end of a line to suppress the printing of output. This is useful when doing calculations which generate long output you are not interested in seeing. The `_` variable and the `_out_` list do get updated with the contents of the output, even if it is not printed. You can thus still access the generated results this way for further processing.

Example:

```
pry(main)> "test" * 1000000
=> "testtesttesttesttest...test"
pry(main)> "test" * 1000000;
pry(main)>
```

<a href="#Back_to_top">![Back][top] Back</a>

<a name="Show_input_buffer">

### Show the contents of the input buffer

When entering long multi-line expressions it can be useful to see the Ruby code you've entered so far. The `show-input` command will display the contents of the input buffer with syntax highlighting and line numbers. This command can be very useful when combined with the `amend-line` and `play` commands.

Example:

```
pry(main)> def poem
pry(main)*   puts "I remember silver hours and sunlight by the rivers"
pry(main)*   puts "And our kisses standing on the spicy plains"
pry(main)* show-input
1: def poem
2:   puts "I remember silver hours and sunlight by the rivers"
3:   puts "And our kisses standing on the spicy plains"
pry(main)*
```

<a href="#Back_to_top">![Back][top] Back</a>

<a name="Amend_line">

### Amend lines of input

When entering multi-line expressions sometimes a mistake may be made on an earlier line. Using amend-line you can specify which line in the input buffer you wish to replace with a corrected version. The syntax for replacing a line is `amend-line N replacement code` where `N` is the number of the line (or range of lines using `A..B` syntax) you wish to replace. The `amend-line` command is usually used in concert with `show-input` command (discussed above).

Example:

```
pry(main)> def hello
pry(main)*   puts "hello #{name}"
pry(main)* amend-line 1 def hello(name)
1: def hello(name)
2:   puts "hello #{name}"
```

Aside from replacing lines, `amend-line` also allows you to delete lines. Simply pass `!` as the replacement code and the line(s) will instead be deleted.

Example:

```
pry(main)> def goodbye
pry(main)*   puts "good evening"
pry(main)*   puts "au revoir"
pry(main)* amend-line 2..3 !
1: def goodbye
pry(main)*
```

As a further time saving feature, if the line number is neglected then
it is the preceding line that is amended.

<a href="#Back_to_top">![Back][top] Back</a>

<a name="Edit_input_buffer">

### Editing the contents of the input buffer with an editor

When the [`edit`](https://github.com/pry/pry/wiki/Editor-Integration#wiki-Edit_command) command is invoked (with no arguments) in a non-empty
input buffer, your editor will open a temp file with the buffer contents. When you later save and exit the editor, the input
buffer will be replaced with the contents of the temp file.

This is great for when you're entering long input, which can become
unwieldy in a REPL. It is also an alternative to [`amend-line`](https://github.com/pry/pry/wiki/User-Input#wiki-Amend_line) as you can
correct any input errors while in the editor.

Example: We invoke an editor in an input buffer and rearrange the lines and add a new one

```
pry(main)> def lorca
pry(main)*   puts "because dawn will throw fistfuls of ants at me"
pry(main)*   puts "cover me at dawn in a veil"
pry(main)* edit
Waiting for Emacs...  <editing takes place here in an external editor>
pry(main)* show-input
1: def lorca
2:   puts "cover me at dawn in a veil"
3:   puts "because dawn will throw fistfuls of ants at me"
4:   puts "and sprinkle my shoes with hard-water so the pincers of the scorpion slide"
pry(main)* 
```

<a href="#Back_to_top">![Back][top] Back</a>

<a name="Replay_history">

### Replay history

A line (or multiple lines) of Readline history can be replayed by
using the `hist --replay` command. The `hist` command will be
discussed in more detail in the [history section](https://github.com/pry/pry/wiki/History), but here is a simple example of its use:

Example:

```
pry(main)> hist --tail 5
4870: puts "hello world"
4871: puts "goodbye cruel world"
4872: puts "evening sir"
4873: 5 + 4
4874: hist --tail 5
pry(main)> hist --replay 4870..4871
hello world
=> nil
goodbye cruel world
=> nil
pry(main)>
```

As an alternative to using `hist --replay` you can use `hist -n` (to display history without line numbers) and then copy/paste the selected history items back into Pry. This may be faster than the `--replay` approach, especially if history is large (and numbers have many digits):

```
pry(main)> hist -n --tail 5 
puts "hello world"
puts "goodbye cruel world"
puts "evening sir"
5 + 4
hist -n --tail 5
```

Select the lines you wish to copy from above, and paste them into Pry to replay them.

<a href="#Back_to_top">![Back][top] Back</a>

<a name="Play">

![play](http://img-fotki.yandex.ru/get/5647/98991937.11/0_92f8f_10dd7a9a_orig)

### Play files and methods as input

The `play` command enables you to replay code from files and methods as if they were entered directly in the Pry REPL. When `play` is not passed any switches a valid method name must be given (in `ri` method syntax). However, you can combine this with the `--open` switch: then the entire method is replayed except for the final line, effectively leaving it “open” for modification using `amend-line`. Alternatively, you can pass a file name instead of a method name. Pry will handle it automatically. The optional `--lines A..B` option can be supplied, which selects the line (or range of lines) to play from the given source.

The following options are supported:

* Use the `-l` option to restrict a subset of lines to be played
* Use the `-o` option to play Pry's output history
* Use the `-i` option to play Pry's input history. This can only replay pure Ruby code, not Pry commands.
* Use the `-s` option to select the super method. Can be repeated to traverse the ancestors
* Use the `-d` option to play documentation for methods
* Use the `--open` option to play the selected content except the last line (which is usually `end`). `amend-line` can then be used to modify the method

Example ([recorded](http://showterm.io/ccdcb0813e82c922a8881)): playing lines in the current context (given a single line) (with help of `-l` or `--lines` flag)

```
[1] pry(main)> load 'play.rb'

Frame number: 0/17

From: /home/curacao/play.rb @ line 2 Object#hi_there_handsome:

    1: def hi_there_handsome
 => 2:   binding.pry
    3:   puts '1', '2'
    4:   puts '3', '4'
    5: end

[1] pry(main)> play -l 3
1
2
=> nil
[2] pry(main)>
```

Example ([recorded](http://showterm.io/08dae786b1d6b61301ebb)): playing lines in the current context (given a range) (with help of `-l` or `--lines` flag)

```
[1] pry(main)> load 'play.rb'

Frame number: 0/17

From: /home/curacao/play.rb @ line 2 Object#hi_there_handsome:

    1: def hi_there_handsome
 => 2:   binding.pry
    3:   puts '1', '2'
    4:   puts '3', '4'
    5: end

[1] pry(main)> play -l 3..4
1
2
3
4
=> nil
[2] pry(main)>
```

Example ([recorded](http://showterm.io/6e75130038730d760f91f)): playing lines of a class (with help of `-l` or `--lines` flag)

```
[1] pry(main)> show-source Pry -b

From: /home/curacao/.gem/ruby/1.9.3/gems/pry-0.9.12.1/lib/pry/pry_instance.rb @ line 1:
Class name: Pry
Number of monkeypatches: 8. Use the `-a` option to display all available monkeypatches
Number of lines: 740

  1: class Pry
  2: 
  3:   attr_accessor :input
...

[2] pry(main)> play Pry -l 63
=> []
[3] pry(main)> @binding_stack
=> []
[4] pry(main)>
```

Example ([recorded](http://showterm.io/d695ff17ee81dd6a9faa9)): playing lines of a Pry command (with help of `-l` or `--lines` flag)

```
[1] pry(main)> respond_to?(:default_file, true)
=> false
[2] pry(main)> show-source play -b

From: /home/curacao/.gem/ruby/1.9.3/gems/pry-0.9.12.1/lib/pry/commands/play.rb 
Number of lines: 65

 1: class Command::Play < Pry::ClassCommand
 2:   match 'play'
 3:   group 'Editing'
...

[3] pry(main)> play play -l 54..56
=> nil
[4] pry(main)> respond_to?(:default_file, true)
=> true
[5] pry(main)> 
```

Example ([recorded](http://showterm.io/6fd34ddfcf1a222f43743)): playing documentation for methods (with help of `-d` or `--doc` flag)

```
[1] pry(main)> cat play.rb
# a = 'h'
# a << 'a'
# a << 't'
# a << 'e'
def love
end
[2] pry(main)> load 'play.rb'
=> true
[3] pry(main)> play -d love
=> "hate"
[4] pry(main)>
```

Example ([recorded](http://showterm.io/870b23c0af8233c706438)): playing documentation for methods (with a subset of lines) (with help of `-d` or `--doc` flag)

```
[1] pry(main)> cat play.rb
# a = 'h'
# a << 'a'
# a << 't'
# a << 'e'
def love
end
[2] pry(main)> load 'play.rb'
=> true
[3] pry(main)> play -d love -l 1..2
=> "ha"
[4] pry(main)>
```

Example ([recorded](http://showterm.io/8f4f1a85465d644c1879d)): selecting lines from output history (with help of `-o` or `--out` flag)

```
[1] pry(main)> str = "world"
=> "world"
[2] pry(main)> "Hello, #{str}"
=> "Hello, world"
[3] pry(main)> play -o 2
=> "Hello, world"
[4] pry(main)>
```

Example ([recorded](http://showterm.io/e90f1df534c9816ed3b5f)): selecting lines from input history (with help of `-i` or `--in` flag)

```
[1] pry(main)> a = b = c = d = 0
=> 0
[2] pry(main)> a += 1
=> 1
[3] pry(main)> b += 1
=> 1
[4] pry(main)> c += 1
=> 1
[5] pry(main)> d += 1
=> 1
[6] pry(main)> play -i 2..4
=> 2
[7] pry(main)> a
=> 2
[8] pry(main)> b
=> 2
[9] pry(main)> c
=> 2
[10] pry(main)> d
=> 1
[11] pry(main)>
```


Example ([recorded](http://showterm.io/a228821eacfa48faf2482)): playing a superclass method (with help of `-s` or `--super` flag)

```
[1] pry(main)> cat play.rb
class A
  def hi
    123
  end
end

class B < A
  def hi
    345
  end
end
[2] pry(main)> load 'play.rb'
=> true
[3] pry(main)> play B#hi -s -l 2
=> 123
[4] pry(main)>
```

The `play` command is quite powerful and there are many use cases. Some include:

Example ([recorded](http://showterm.io/afdb157c63058a433f7e4)): stitching together new methods from bits and pieces of other methods

```
[1] pry(main)> def get_input
[1] pry(main)*   puts "Enter something:"
[1] pry(main)*   var = $stdin.gets
[1] pry(main)* end  
=> nil
[2] pry(main)> def greeting
[2] pry(main)*   puts "How are you today?"
[2] pry(main)* end  
=> nil
[3] pry(main)> def stitched_method
[3] pry(main)*   play greeting -l 2
1: def stitched_method
2:   puts "How are you today?"
[3] pry(main)*   play get_input -l 3
1: def stitched_method
2:   puts "How are you today?"
3:   var = $stdin.gets
[3] pry(main)*   puts "That's great you're feeling #{var.chomp}!"
[3] pry(main)* end  
=> nil
[4] pry(main)> show-method stitched_method

From: (pry) @ line 8:
Owner: Object
Visibility: private
Number of lines: 5

def stitched_method
  puts "How are you today?"
  var = $stdin.gets
  puts "That's great you're feeling #{var.chomp}!"
end
[5] pry(main)>
```

Example ([recorded](http://showterm.io/67e398e1a7a0c08702f50)): extract lines of code out of a file you're browsing:

```
[1] pry(main)> cat FAQ.md -l -s 18 -e 25
18: ```ruby
19: begin
20:   require 'awesome_print'
21:   Pry.config.print = proc { |output, value| output.puts value.ai }
22: rescue LoadError => err
23:   puts "no awesome_print :("
24: end
25: ```
[2] pry(main)> play #{_file_} -l 19..24
no awesome_print :(
=> nil
[3] pry(main)>
```

Note that in the above we are interpolating the [special local](https://github.com/pry/pry/wiki/Special-Locals#wiki-Last_file_and_dir) `_file_`
into the `play` command: see more on interpolation [here](https://github.com/pry/pry/wiki/Command-system#wiki-Command_interpolation).

We have just scratched the surface of what's possible with
the `play` command - the combination of `play`, `amend-line` and
`show-input` form a trilogy of commands that work together to support
some very powerful possibilities. Other possibilities including:
transplanting methods from one context to another, and re-opening
methods (using `play -m meth --open`) for easy monkey-patching
directly in Pry.

<a href="#Back_to_top">![Back][top] Back</a>

<a name="Inp_out_cache">

### The `_in_` and `_out_` cache

Input expressions and output results are automatically stored in array-like data structures
accessible from the `_in_` and `_out_` locals respectively. 

For more information see the [Special locals](https://github.com/pry/pry/wiki/Special-Locals#wiki-In_and_out) section.

<a href="#Back_to_top">![Back][top] Back</a>

[top]: http://img-fotki.yandex.ru/get/5638/98991937.10/0_8ed2c_2c7b9e70_orig