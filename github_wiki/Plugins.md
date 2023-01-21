# Plugins

<a name="Back_to_top">

### Quick Menu:

* <a href="#Plugin_what_is">What is a plugin?</a>
* <a href="#Load_suppress">Loading and suppression (all plugins)</a>
* <a href="#Load_suppress_per_plugin">Loading and suppression (per plugin)</a>
* <a href="#Plugin_listing">List installed plugins</a>

[Back to Main Menu](https://github.com/pry/pry/wiki)

### Overview

Pry can be extended by way of plugins. A plugin is very simple - it is
just a gem that is automatically loaded  when a Pry session starts.
Plugins can be used to modify almost any aspect of Pry.

For a list of available plugins check out the [Available Plugins](https://github.com/pry/pry/wiki/Available-plugins) wiki page.


<a name="Plugin_what_is">

### What is a plugin?

A valid Pry plugin is a gem that has the `pry-` prefix (such as the
[`pry-doc`](https://rubygems.org/gems/pry-doc) gem). There must also
be a `.rb` file of the same name in the `lib/` folder of the gem. The
functionality provided by a plugin is typically implemented by way of the
[customization](https://github.com/pry/pry/wiki/Customization-and-configuration),
[hooks](https://github.com/pry/pry/wiki/Hooks), and
[command system](https://github.com/pry/pry/wiki/Command-system) APIs.

In the following example, assume we have created a gem called
`pry-sample` that has the file `pry-sample.rb` in the
`lib/` folder.

Example: Contents of `lib/pry-sample.rb`

```ruby
Pry.config.prompt = proc { "Sample Plugin Prompt> " }
```

From above, the `pry-sample` gem is a valid Pry plugin. Once it
is installed it will be required automatically by Pry and the Pry
prompt will be set to `Sample Plugin Prompt>`

<a href="#Back_to_top">Back to top</a>

<a name="Load_suppress">

### Loading and suppression (all plugins)

If a Pry plugin is installed (i.e a gem with the `pry-` prefix is installed) it will be
loaded automatically when a session starts. As stated in the [`.pryrc`
section](https://github.com/pry/pry/wiki/Pry-rc#Load_suppress),
plugins are loaded _after_ the `.pryrc` file is loaded but _before_
history is loaded; this means that plugin loading can be controlled in
the `.pryrc` file; it also means that plugins can control the
configuration of [history](https://github.com/pry/pry/wiki/History).

As plugins are loaded automatically, suppression of plugin
loading must be explicit:

**Command line suppression**

From the command-line all plugin loading can be suppressed using the
`--no-plugins` switch:

Example: Suppress all plugin loading from command line

```
crow:~ john$ pry --no-plugins
```

**Runtime suppression**

If [invoking Pry at runtime](https://github.com/pry/pry/wiki/Runtime-invocation) you can
disable Plugin loading by using the `Pry.config.should_load_plugins`
configuration option:

Example: Disable plugin loading at runtime

```ruby
Pry.config.should_load_plugins = false
```

<a href="#Back_to_top">Back to top</a>

<a name="Load_suppress_per_plugin">

### Loading and suppression (per plugin)

For finer grained control over plugins, individual plugin loading can
be turned on and off. The following can be performed in either your
`.pryrc` file or in the code before you start your session (when
invoking at runtime).

**Disabling a specific plugin**

An individual plugin can be disabled (leaving other plugins enabled) by using the `Pry.plugins` API:

Example: Disable only the `pry-doc` plugin

```ruby
Pry.plugins["doc"].disable!
```

**Forcing activation of specific plugins**

Alternatively, if you invoke the Pry executable with the
`--no-plugins` switch you can still activate specific plugins.

Example:

```ruby
Pry.plugins["doc"].activate!
```

<a href="#Back_to_top">Back to top</a>

<a name="Plugin_listing">

### List installed plugins

Simply invoke the Pry executable with the `--plugins`
switch.

Example:

```
crow:~ john$ pry --plugins
Installed Plugins:
--
doc               Provides YARD and extended documentation support for Pry
git               A Ruby-aware git layer
crow:~ john$
```

<a href="#Back_to_top">Back to top</a>