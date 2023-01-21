# Editor integration

<a name="Back_to_top">

### Quick Menu:

* <a href="#Edit_command">Using the `edit` command</a>
* <a href="#Set_editor">Setting the default editor</a>
* <a href="#Shell_editor">Invoking an editor through the shell</a>
* [Integrating Pry into Emacs](ruby-dev.el)

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

Pry has a shell-like prompt that can do some things an editor can't (like tab-complete actual methods of an object), but sometimes you'll need to use a traditional editor.

<a name="Edit_command">

### Using the `edit` command

The `edit` command is used to invoke your <a href="#Set_editor">default editor</a>. This command
will `load` your file once you have finished editing it (unless you pass the `-n` or `--no-reload` flag to `edit`).

Pry also has some short-cuts for opening files you might need.

The `edit` command, as well as changing Pry's [recent input](User-Input#wiki-Edit_input_buffer), can be used to open any file. You can use `edit` with:

* The `-c` switch to open the 'current' file/line (as shown by `whereami` command, such as when Pry is invoked inside a method using `binding.pry` at [runtime](https://github.com/pry/pry/wiki/Runtime-invocation)).
* `Class#a_method` or `Class.a_class_method` (or `my_method` if the method is in scope) to open a method for editing directly in the default editor. Pry will attempt to jump to the line in the file where the method is defined.
** This accepts the <a href="#p-flag">`-p` switch</a> to open a temporary file containing only the method, then patch that edit in (without modifying the actual file).
* `<filename>` or `<filename>:<line-number>`, to specify which file to open.
* The `--in 1..2` flag to specify a range of the Pry input buffer to edit.
* The `-t` switch to open a temporary empty file in an editor.
* The `--ex` switch to open the relevant file at the line that generated the last exception.

    <small>HINT: Use `--ex N` if you want the Nth line of the backtrace, just like [cat --ex](Shell-Integration#wiki-Cat).</small>
* The `-l` switch to jump to the specified line number.
* The `-n` switch to stop the automatic reloading of `.rb` files after you have edited them.
* The `-r` switch to force Pry to reload and eval a file, even if it does not end in `.rb`.

For additional information on the `edit --ex` functionality, see the [Exception handling](https://github.com/pry/pry/wiki/Exceptions#wiki-edit___ex) section.

Being run without any arguments, the `edit` command modifies the last input expression.

Example: Jump to line 10 in the file, Pry will reload the file after you have finished

```
pry(main) edit hello.rb:10
```

Example: `edit --ex` causes the file `blah.rb` to be opened at line 7

```
pry(main)> hello
NameError: undefined local variable or method `error' for main:Object
from /Users/john/blah.rb:7:in `hello'
pry(main)> edit --ex
```

Example: `edit -n` stops Pry from reloading the file.

```
pry(main)> edit -n ../other-project/other_project.rb
```

<small>HINT: if you're peeking into files belonging to other projects, you should consider using the [cat command](Shell-Integration#wiki-Cat) instead.</small>


* Use the `-M` switch to edit methods defined on instances of a class.
* Use the `-m` switch to edit methods defined on the object itself.
* Use the `-s` option to select the super method. You can repeat `-ss` to get to the super method's super method.
* Use the `-n` switch to prevent reloading (using `load`) of the file's
  contents after editing.
* Use `--no-jump` to not fast forward editor to first line of method.

<a name="p-flag"></a>
Example: use `-p` to fix a pig's vocal organ:

```ruby
# pig.rb

class Pig
  def say_hello
    :woof
  end

  def eat
    "Om nom nom"
  end
end

piggy = Pig.new

binding.pry
```

```
> ruby -rpry pig.rb
pry(main)> piggy.say_hello
=> :woof
pry(main)> edit -p Pig#say_hello
```

Edit the `say_hello` method in your text editor:

![Editing #say_hello](http://img-fotki.yandex.ru/get/6521/98991937.e/0_87640_82501400_orig)

```
pry(main)> new_piggy = Pig.new
pry(main)> new_piggy.say_hello
=> :oink
```

(Note that this didn't change `pig.rb`, only an in-memory version of it.)

Example:

```
pry(main)> edit -n Grit::Git#apply_patch
```

<a href="#Back_to_top">Back to top</a>

<a name="Set_editor">

### Setting the default editor

As an alternative to invoking an editor through the shell you can also
set a default editor for Pry.

The `Pry.config.editor` variable defaults to the environment variable `$VISUAL` or `$EDITOR` (in that order). If neither of them is defined, it will try to use the default editor of your platform (usually `nano`). If you set it to a `String` then that string is used as the shell
command to invoke the editor. If you set it to a callable (e.g a `Proc`) then file and line are passed in as parameters and the
return value of that callable invocation is used as the exact shell command to invoke the editor.

The value of `Pry.config.editor` is then used by commands such as `edit`.

Note that it maybe be convenient to set your desired editor in the `.pryrc` file or else
in the `$VISUAL` or `$EDITOR` environment variable.

Example: Setting a String

```ruby
Pry.config.editor = "emacsclient"
```

Example: Setting a proc

```ruby
Pry.config.editor = proc { |file, line| "emacsclient +#{line} #{file}" }
```

_**Note: Pry cannot translate a shell alias into a command because of the way Ruby works, so if you use an alias such as 'vi=vim' set your `EDITOR` env to vim and not vi.**_

<a href="#Back_to_top">Back to top</a>

<a name="Shell_editor">

### Invoking an editor through the shell

This is the simplest way to start an editor from Pry. As stated in the
[shell integration
section](https://github.com/pry/pry/wiki/Shell-Integration) any input
prefixed by a `.` is sent to the shell; so one way to open an editor
in Pry is just to enter the name of the editor prefixed by a `.`

Example:

```
pry(main)> .vim test_file.rb
```

Example: Interpolate file name

```
pry(main)> _file_
=> "/Users/john/.rvm/gems/ruby-1.9.2-p180/gems/grit-2.4.1/lib/grit/blob.rb"
pry(main)> .emacsclient #{_file_}
```

<a href="#Back_to_top">Back to top</a>
