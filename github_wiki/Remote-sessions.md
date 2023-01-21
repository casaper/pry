<a name="Back_to_top">
[Back to Main Menu](/pry/pry/wiki)

### Quick Menu:

* <a href="#installation">Installation</a>
* <a href="#usage">Usage</a>
* <a href="#limitations">Limitations</a>
* <a href="#credits">Credits</a>

### Overview

Using the `pry-remote` gem we can start Pry remotely and connect to it using DRb. This allows us to
access the state of the running program from anywhere. 

This also makes Pry usable with tools such as [Pow](http://pow.cx/).

<a name="installation"></a>
### Installation

    gem install pry-remote

<a name="usage"></a>
### Usage

Here's a program starting pry-remote, we simply use `binding.remote_pry` instead of the normal `binding.pry`:

```ruby
    require 'pry-remote'

    class Foo
      def initialize(x, y)
        binding.remote_pry
      end
    end

    Foo.new 10, 20
```

Running it will prompt you with a message telling you Pry is waiting for a
program to connect itself to it:

     [pry-remote] Waiting for client on drb://localhost:9876

You can then connect yourself using the ``pry-remote`` executable:

    $ pry-remote
    From: example.rb @ line 7 in Foo#initialize:
         2:
         3: require 'pry-remote'
         4:
         5: class Foo
         6:   def initialize(x, y)
     =>  7:     binding.remote_pry
         8:   end
         9: end
        10:
        11: Foo.new 10, 20
    pry(#<Foo:0x00000000d9b5e8>):1> self
    => #<Foo:0x1efb3b0>
    pry(#<Foo:0x00000001efb3b0>):1> ls -l
    _  _dir_  _ex_  _file_  _in_  _out_  _pry_  x  y
    pry(#<Foo:0x00000001efb3b0>):1> ^D

<a name="limitations"></a>
### Limitations

Paging is turned off. Tab completion does not work.

The `edit` command will not work for terminal editors; and will not work at all when the DRb client/server reside on different computers.

These limitations will be addressed in later releases.

<a name="credits"></a>
### Credits

`pry-remote` is a project by [Mon Ouie](https://github.com/mon-ouie). See the actual project page [here](https://github.com/mon-ouie/pry-remote).


<a href="#Back_to_top">Back to the top of the page</a>
