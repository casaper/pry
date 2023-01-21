<a name="Back_to_top">
<a href="/pry/pry/wiki">Back to Main Menu</a>

### Quick Menu:

* <a href="#pry_rails">With the pry-rails plugin (Rails3, 4+ only) - recommended</a>
* <a href="#with_gemfile">With Pry in your Gemfile</a>
* <a href="#without_gemfile">Without Pry in your Gemfile</a>
* <a href="#rails32up">Rails 3.2+ fix</a>
* <a href="#with_irbfile">With ~/.irbrc file</a>
* <a href="#heroku">On Heroku</a>

### Overview
This page will show you how to configure your Rails application to use Pry instead of IRB.

<a name="pry_rails"></a>
### With the pry-rails plugin (Rails3, 4+ only) - recommended

This is a gem which causes `rails console` to open `pry` as well as 
integrating with the Rails framework to provide additional features to
enhance your experience, such as commands like `show-models`, `show-routes`, 
and more!

Add this line to your gemfile:

`gem 'pry-rails', :group => :development`


`bundle install` and enjoy!

See the [pry-rails](https://github.com/rweng/pry-rails) project page for more information.

<a name="with_gemfile"></a>
### With Pry in your Gemfile

* Rails2

  Run the following to setup pry in Rails 2
  `./script/console --irb=pry`

* Rails3

  **If you're using Rails3, the best solution is to also use the pry-rails gem above.**

  Add the following as a initializer - it provides you with access to the entire Rails stack, 
  and all the useful Rails3 console helpers. After the initializer is in place, you can go ahead and load the console normally using `rails console`.

```ruby
MyApplication::Application.configure do
  # Use Pry instead of IRB
  silence_warnings do
    begin
      require 'pry'
      IRB = Pry
    rescue LoadError
    end
  end
end
```

<a name="without_gemfile"></a>
### Without Pry in your Gemfile.

to use pry with a rails application without adding a 'pry' dependency to your Gemfile you can copy&paste
this code to `$RAILS_APP/.pryrc`. the helper methods rails provides to IRB(like `reload!`) are also made
available inside pry. this works as a replacement to `rails console` or `./script/console` but does not allow you to start pry at runtime inside a rails application. you can start pry with a rails app loaded at a shell by calling '$ pry' inside the working directory of a rails app.

```ruby
rails = File.join Dir.getwd, 'config', 'environment.rb'
if File.exist?(rails) && ENV['SKIP_RAILS'].nil?
  require rails
  if Rails.version[0..0] == "2"
    require 'console_app'
    require 'console_with_helpers'
  elsif Rails.version[0..0] == "3"
    require 'rails/console/app'
    require 'rails/console/helpers'
  else
    warn "[WARN] cannot load Rails console commands (Not on Rails2 or Rails3?)"
  end
end
```

<a name="rails32up"></a>
### A minor fix needed on Rails 3.2+

You may find that the console helpers (`reload!` et al.) are missing from pry in Rails 3.2+ if you are not using the `pry-rails` gem. As a workaround, add something like this to your `.pryrc`:

```rails
if defined?(Rails) && Rails.env
  extend Rails::ConsoleMethods
end
```

<a name="with_irbfile"></a>
### With ~/.irbrc file 

This approach simply piggy-backs on IRB.
If you're using a Gemfile, you should add `gem 'pry', :group => :development` to it.

This technique is discussed in more depth [here](http://lucapette.me/pry-everywhere/).

* Just add the lines below into your ~/.irbrc file.

```ruby
begin
  require "pry"
  Pry.start
  exit
rescue LoadError => e
  warn "=> Unable to load pry"
end
```

<a name="heroku"></a>
### On Heroku

**Heroku users with Rails apps, do not need to do this, this method is reserved for people using Sinatra or EM as their framework. For Rails you simply need to add pry-rails and pry to your Gemfile, bundle install and then push and run `heroku run console` and it will load pry with your Rails environment as expected since Heroku can detect Rails apps and is designed to work well with them**

#### Heroku w/ a Sinatra or EM app.

To convince Heroku to use Pry as the console instead of IRB add `gem 'pry'` to your Gemfile and create a file called pry with.

```ruby
#!/usr/bin/env ruby
# To launch push this into your Git then do:
# heroku run console pry
# heroku console pry
require 'pry'
pry
```

After you have the above, commit it and push it, and then you can do `heroku run console pry` If you would like to shortcut it (because you are forgetful or otherwise) you can create a file called heroku in your home folder bin (make sure to change the path to your gem bin unless you are on Ubuntu 11.04+ or Debian unstable+) with the following:

```bash
#!/usr/bin/env bash
# chmod +x
set -e

if [ "$2" == "console" ]; 
then
  exec /usr/local/bin/heroku run console pry
else
  /usr/local/bin/heroku $@
fi
```

<a href="#Back_to_top">Back to the top of the page</a>