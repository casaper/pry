# The pryrc file

<a name="Back_to_top">

### Quick Menu:

* <a href="#Load_suppress">Loading and suppression</a>
* <a href="#Customize">Customize Pry in your `pryrc`</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

The `pryrc` file is analogous to the `.irbrc` file for IRB.  You can use the `pryrc` file to customize Pry.

When pry starts, it checks for a `pryrc` file in `$XDG_CONFIG_HOME/pry/pryrc` or in your home directory (`~/.pryrc`), and also for a **per-project** `.pryrc` in the current directory(`./.pryrc`). Both files are used if they exist, with the file from your home directory being loaded first.

<a name="Load_suppress">

### Loading and suppression

The `pryrc` files are the first files loaded after Pry is initialized but before a session starts.  Notably, the `pryrc` files are loaded *before* plugins and Readline history are loaded, giving you an opportunity to control these two features.

Suppression of `pryrc` from loading:

* Command line suppression of `pryrc`:  Invoke the `pry` executable with the the `-f` switch.
* Runtime invocation and suppression of `pryrc`:  Ensure that you set `Pry.config.should_load_rc = false` before starting a session.

Example: Command line invocation and pryrc suppression

```ruby
crow:~ john$ pry -f
pry(main)>
```

Example: Runtime invocation and pryrc suppression

```ruby
Pry.config.should_load_rc = false
binding.pry
```

<a href="#Back_to_top">Back to top</a>

<a name="Customize">

### Customize Pry in your pryrc

Nearly every aspect of Pry can be customized in the `pryrc` file; we can change the prompt, add command aliases, add or delete commands, disable certain plugins, configure Readline history loading, turn off colors and paging, and so on.

In the example below we
[`import`](https://github.com/pry/pry/wiki/Command-system#Command_import)
the Experimental command set, turn off paging and color, add a command alias, define a new
command, turn off history saving, and change the prompt (these options
explained further in the
[customization](https://github.com/pry/pry/wiki/Customization-and-configuration)
and [commands](https://github.com/pry/pry/wiki/Command-system) sections):

Example:

```ruby
Pry.config.commands.import Pry::ExtendedCommands::Experimental

Pry.config.pager = false

Pry.config.color = false

Pry.config.commands.alias_command "lM", "ls -M"

Pry.config.commands.command "add", "Add a list of numbers together" do |*args|
  output.puts "Result is: #{args.map(&:to_i).inject(&:+)}"
end

Pry.config.history.should_save = false

Pry.config.prompt = [proc { "input> " },
                     proc { "     | " }]

# Disable pry-buggy-plug:
Pry.plugins["buggy-plug"].disable!
```


<a href="#Back_to_top">Back to top</a>





