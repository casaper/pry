Like any open-source project, we strongly encourage people to help us make Pry better. There are various ways you can help — whatever you are good at! While we're always happy for new code, or fixed bugs, we also need to maintain the wiki pages and the bug-tracker.

If you'd like to help, but don't know what to do, please come and ask in the [IRC channel](#IRC_channel).

### Setting up a development environment

There are a few things you'll need to do to work on Pry.

1. (Optional-ish) Install [rvm](http://beginrescueend.com/). `$ bash < <(curl -s https://rvm.beginrescueend.com/install/rvm)`
2. Get a copy of the source code. If you're planning to make changes, do this by clicking "Fork" on [Github](https://github.com/pry/pry),

    and then `git clone git@github.com:your-name/pry.git`.
3. Install bundler and the gems that Pry needs. `gem install bundler && bundle install`

To test whether this has worked, you should now try running `rake pry` inside the `pry` directory. That will start the current development version of Pry, which you can play around in. If you're having difficulties, please jump into the [IRC channel](#IRC_channel), and we'll try to lend you a hand.

### Contributing code

We don't take process too seriously, but ideally you should follow this pattern for fixing bugs, or for writing features:

1. Write some code that does what you want it to do. We roughly follow [this style guide](https://github.com/bbatsov/ruby-style-guide), please try to err on the side of clear code, informative comments and small commits :).
2. [Write some tests](#Writing_tests), to make sure that no-one breaks your feature, or un-fixes your bug.
3. [Run the tests](#Running_tests) that we already have, to make sure you didn't break any of our features.
4. Push the code to your copy of the Pry repository.
5. Open a [pull request](https://github.com/pry/pry/pulls). If you are addressing one of the existing [issues](https://github.com/pry/pry/issues), please mention that in the title of the pull request.

### Running tests

Whenever you write new code for Pry, you should run the tests, to check that you haven't accidentally broken something that used to work. Running the tests should be very easy:

1. Run `rake spec` inside the `pry` directory.

If you don't want to see colourful output then set `NO_PRY_COLORED_BACON` environment variable in your `.zshrc`, `.bashrc` or other configuration file for your favourite shell.

Example

```
# ~/.zshrc
# ...
export NO_PRY_COLORED_BACON=true
# ...
```

If you're writing code that does something complicated, please try to run the tests under all supported versions of Ruby. This is most easily done using rvm to install each necessary version of Ruby, and then using `rvm rake test`. You will have to install the necessary gems for each version of Ruby.

At the moment, the supported versions of Ruby are `1.9.2+` and `jruby`.

### Writing tests

The large range of Ruby's on which we try to run makes it unfortunately easy to break one feature in one version. Without the tests, we'd never notice, so you should always include tests whenever you add something new, or fix something that's broken.

At the moment we're using the [bacon](http://www.ruby-forum.com/topic/137928) test framework, though that may change before you read this...

Many of our tests are "integration" tests, which means that instead of trying to unit-test a tiny part of Pry, we test the entire user-workflow. For example to check that when the user types "1 +", and then "1", they will still get "2", you might write a test like:

```ruby

  describe "pry" do
    it "should allow a line-break after operators" do
      mock_pry("1 + ", "1").should =~ /2/
    end
  end
```

This gives us flexibility to refactor large parts of Pry internally, while making sure that what the user sees when they type anything stays the same. If the code you are writing can be unit-tested as well, that is also a very good way to write tests, so please feel free to do that too!

### IRC channel

Almost everyone who works on Pry spends time in the [#pry](irc://freenode.net/#pry) IRC channel, which is hosted on [Freenode](https://freenode.net). Please feel free to come and report bugs, discuss features, or just have fun.

As we're quite distributed around the world, there's no guarantee anyone will be awake, but please hang around.