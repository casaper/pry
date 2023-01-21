# Managing History

<a name="Back_to_top">

### Quick Menu:

* <a href="#History_file">The history file</a>
* <a href="#Loading_history">Loading history</a>
* <a href="#Saving_history">Saving history</a>
* <a href="#Hist_overview">The hist command (overview)</a>
* <a href="#Hist_grep">The `grep` option</a>
* <a href="#Hist_tail">The `tail` option</a>
* <a href="#Hist_head">The `head` option</a>
* <a href="#Hist_no_numbers">The `no-numbers` option</a>
* <a href="#Hist_replay">The `replay` option</a>
* <a href="#Hist_exclude">The `exclude` option</a>
* <a href="#Hist_save">The `save` option</a>
* <a href="#Hist_show">The `show` option</a>
* <a href="#Hist_clear">The `clear` option</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

The ability to control history is a useful feature in itself, but it
becomes indispensable when your REPL sessions become large or
complicated. As a result, Pry provides rich history management, enabling you to load, save,
view, and replay history, among other things.

<a name="History_file">

### The history file

As stated in the <a href="https://github.com/pry/pry/wiki/Customization-and-configuration#Config_history">customization</a> section
the `Pry.config.history_file` option can be used to set the history
file - the file where history is saved to and loaded from.

Note that because both the [.pryrc file](https://github.com/pry/pry/wiki/Pry-rc)
and [plugins](https://github.com/pry/pry/wiki/Plugins) are loaded
before the history file, you can change the history options in either
of these places. You can also change it any point before you start Pry when [invoking it at runtime.](https://github.com/pry/pry/wiki/Runtime-invocation)

The default history file is `~/.pry_history`

Example: Use irb's history file instead

```ruby
Pry.config.history_file = "~/.irb_history"
```

<a href="#Back_to_top">Back to top</a>

<a name="Loading_history">

### Loading history

The `Pry.config.history_load` option determines whether history
will be loaded from the history file when
a Pry session starts. This option can be modified in any of the places
given in the <a href="#History_file">history file</a> section,
above.

Loading history will pre-populate the `Readline::HISTORY` array with
the content in the history file.

The default value is `true`

Example: Disable history loading

```ruby
Pry.config.history_load = false
```

<a href="#Back_to_top">Back to top</a>

<a name="Saving_history">

### Saving history

The `Pry.config.history_save` option determines whether history
will be saved to the history file when
a Pry session ends. This option can be modified in any of the places
given in the <a href="#History_file">history file</a> section,
above.

The default value is `true`

Example: Disable history saving

```ruby
Pry.config.history_save = false
```

<a href="#Back_to_top">Back to top</a>

<a name="Hist_overview">

### The `hist` command (overview)

The `hist` command is the primary interface to your Pry history. The
`hist` command enables you to view, search, and
replay history, among other things.

Note however that the current version of the `hist` command does not
allow options to stack; that is `hist --tail 10 --grep hello` does not
perform as expected. This feature will be added in a future version.

The `hist` command when invoked by itself with no options will simply
display all Pry history. If there is more than a screenful of history
the output will be sent through a pager.

Example: Display all Pry history

```
pry(main)> hist
0: require 'bacon'
1: $ describe
2: ? describe
3: ls -M Bacon::Context
4: hist
```

<a href="#Back_to_top">Back to top</a>

<a name="Hist_grep">

### The `grep` option

When the `--grep REGEX` option is passed to `hist` only those lines
which match the regular expression will be displayed.

```
pry(main)> hist --grep desc
1: $ describe
2: ? describe
```

<a href="#Back_to_top">Back to top</a>

<a name="Hist_tail">

### The `tail` option

When the `--tail N` option is passed to `hist` only the last `N` lines
of history will be displayed. Note that if the `N` parameter is left
out then `hist` will default to displaying the last 10 lines.

Example:

```
pry(main) hist --tail 5
12:     puts "silk banners, drove of pure lilies"
13:     puts "beneath walls a virgin once defended;"
14:   end
14: end
15: hist --tail 5
```

<a href="#Back_to_top">Back to top</a>

<a name="Hist_head">

### The `head` option

When the `--head N` option is passed to `hist` only the first `N` lines
of history will be displayed. Note that if the `N` parameter is left
out then `hist` will default to displaying the first 10 lines.

Example:

```
pry(main)> hist --head 3
0: require 'bacon'
1: $ describe
2: ? describe
```

<a href="#Back_to_top">Back to top</a>

<a name="Hist_no_numbers">

### The `no-numbers` option

The `--no-numbers` (or `-n`) switch turns off line numbers when displaying readline history. This can be
useful as an alternative to `--replay` as it enables easy copy/pasting of history back into Pry for replaying.

```
[8] (pry) main: 0> hist -n -T
hist -n
hist -T -n
.clear
hist -n -T
def hello
  puts "hi"
end
(1..3).each do |v|
  puts v * 6
end
[9] (pry) main: 0> 
```
<a href="#Back_to_top">Back to top</a>

<a name="Hist_replay">

### The `replay` option

The `--replay A..B` option allows us to replay lines of history. It
accepts either a single line number or a range of numbers. It even
allows replaying of incomplete multi line expressions.

Example:

```
pry(main)> hist --tail 3
7000: puts "hello world";
7001: puts "good evening world";
7002: hist --tail 3
pry(main)> hist --replay 7000
hello world
```

Example: Replaying an incomplete multi-line expression

```
pry(main)> hist --tail 6
7008: class Evening
7009:   def sundown
7010:     puts "goodnight"
7011:   end
7012: end
7013: hist --tail 6
pry(main)> hist --replay 7008..7011
pry(main)*   def moonout
pry(main)*     puts "dog's bark"
pry(main)*   end
pry(main)* end;
pry(main)> Evening.new.moonout;
dog's bark
pry(main)>
```

<a href="#Back_to_top">Back to top</a>

<a name="Hist_exclude">

### The `exclude-pry` option

The `--exclude-pry` option, or `-e`, displays all lines of history except those that
are Pry commands.

Example: Comparision of `hist` with and without `--exclude-pry`

```
pry(main)> hist
0: hist
1: ls -m
2: show-method Gem.ruby
3: hist
4: puts "testing: 1, 2, 3..."
5: 1 + 3
6: hist
pry(main)> hist --exclude-pry
4: puts "testing: 1, 2, 3..."
5: 1 + 3
pry(main)>
```

<a href="#Back_to_top">Back to top</a>

<a name="Hist_save">

### The `save` option

The `--save [A..B] FILE` option allows you to save lines from the
history buffer to a file. If the range is left out the entire history is
saved to the specified file. Note that an implied `--exclude` is
performed on the lines before saving -- only valid Ruby code is saved.

Example:

```
pry(main)> hist
0: hist
1: ls -m
2: show-method Gem.ruby
3: hist
4: puts "testing: 1, 2, 3..."
5: 1 + 3
6: hist
7: hist --exclude
8: hist -h
9: hist
pry(main)> hist --save 4..5 test.rb
Saving history in /Users/john/ruby/projects/pry.wiki/test.rb ...
... history saved.
pry(main)> cat test.rb
puts "testing: 1, 2, 3..."
1 + 3
pry(main)>
```

<a href="#Back_to_top">Back to top</a>

<a name="Hist_show">

### The `show` option

The `--show A..B` options displays the line or range of lines to the
user. May be useful when used after the `--grep` option and before the
`--replay` option.

Example:

```
pry(main)> hist --show 2..5
2: show-method Gem.ruby
3: hist
4: puts "testing: 1, 2, 3..."
5: 1 + 3
pry(main)>
```

<a href="#Back_to_top">Back to top</a>

<a name="Hist_clear">

### The `clear` option

The `--clear` option clears all current session history. 

Example:

```
pry(main)> hist --clear
History cleared.
pry(main)> hist
0: hist
pry(main)>
```

<a href="#Back_to_top">Back to top</a>