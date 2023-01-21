![Alt text](https://www.dropbox.com/s/zp8o63kquby2rln/pry_logo_350.png?raw=1)

**Overview:**

**See the [CHANGELOG](https://github.com/pry/pry/blob/master/CHANGELOG.md) for more information on what's changed**

Welcome to the official wiki for the Pry REPL: a powerful IRB alternative and
runtime developer console for Ruby. Note that not every  aspect of Pry is
covered in the material below, just a very significant chunk. For
basic information on getting started it is recommended you watch the
[introductory screencast](http://vimeo.com/26391171).

At the moment, the supported versions of Ruby are `1.9.2+` and `jruby`. Note that Pry 0.9.X supports Ruby `1.8.7` but Pry 0.10.0+ does not support `1.8.7`.

If you have any problems or queries first check the [FAQ](https://github.com/pry/pry/wiki/FAQ) and if you do not find what you need there [file an issue](http://github.com/pry/pry/issues) or [contact me](http://github.com/banister).

Thanks!

**![Guides](http://img-fotki.yandex.ru/get/6420/98991937.f/0_88d88_e95b3d0f_orig) Guides:**

* [Entering input](https://github.com/pry/pry/wiki/User-Input)
* [Navigating around Pry](https://github.com/pry/pry/wiki/State-navigation)
* [Accessing the system shell](https://github.com/pry/pry/wiki/Shell-Integration)
* [Viewing source code](https://github.com/pry/pry/wiki/Source-browsing)
* [Viewing documentation](https://github.com/pry/pry/wiki/Documentation-browsing)
* [The special locals](https://github.com/pry/pry/wiki/Special-Locals)
* [Managing history](https://github.com/pry/pry/wiki/History)
* [The pryrc file](https://github.com/pry/pry/wiki/Pry-rc)
* [Runtime invocation](https://github.com/pry/pry/wiki/Runtime-invocation)
* [The live help system](https://github.com/pry/pry/wiki/Live-help)
* [Editor integration](https://github.com/pry/pry/wiki/Editor-integration)
* [Customization and configuration](https://github.com/pry/pry/wiki/Customization-and-configuration)
* [Exception handling](https://github.com/pry/pry/wiki/Exceptions)
* [The command system](https://github.com/pry/pry/wiki/Command-system)
* [Creating custom commands](https://github.com/pry/pry/wiki/Custom-commands)
* [Plugins](https://github.com/pry/pry/wiki/Plugins)
* [Hooks](https://github.com/pry/pry/wiki/Hooks)
* [Using Pry with Rails](https://github.com/pry/pry/wiki/Setting-up-Rails-or-Heroku-to-use-Pry)
* [Using Pry in your browser with Rails](https://github.com/mrbrdo/rack-webconsole)
* [Remote sessions](https://github.com/pry/pry/wiki/Remote-sessions)

<a name="Screencasts"></a>
**![Screencasts](http://img-fotki.yandex.ru/get/6421/98991937.f/0_88d87_91bcd7_orig) Screencasts**

* [Joshua Cheek's Introductory screencast](http://vimeo.com/26391171)
* [Pry with Rails (Railscasts)](http://railscasts.com/episodes/280-pry-with-rails)
* [Another introductory screencast on the basics](http://vimeo.com/23535249)
* [Invoking Pry at runtime for basic REPL-oriented development](http://vimeo.com/23634437)
* [HowToCode - Portuguese screencast on Pry](http://f.simplesideias.com.br/howtocode/screencasts/pry/video.mp4)
* [Google I/O 101: YouTube and Ruby on Rails for Education](http://www.youtube.com/watch?v=XstD6_c3hj0&feature=player_detailpage#t=819s)
* [“Debugging Ruby with Pry”](http://knomedia.github.io/blog/2013/01/21/debugging-ruby-with-pry/) (by Jason Madsen, mostly covers the _pry-debugger_ plugin)

**![Conference presentations](http://img-fotki.yandex.ru/get/6621/98991937.f/0_88d86_87dc35f4_orig) Conference presentations**

* [Prying into your app's private life - Corey Woodcox @ MountainWest Rubyconf 2012](http://confreaks.com/videos/959-mwrc2012-prying-into-your-app-s-private-life)
* [Mastering the Ruby debugger - Jim Weirich @ Rocky Mountain Ruby 2011](http://confreaks.com/videos/722-rockymtnruby2011-mastering-the-ruby-debugger)
* [REPL driven development with Pry - Conrad Irwin @ Ruby Conf 2013](http://www.confreaks.com/videos/2864-rubyconf2013-repl-driven-development-with-pry)
* [Debugger Driven Developement with Pry - Joel Turnbull @ Rails Conf 2014](https://www.youtube.com/watch?v=4hfMUP5iTq8)

**![Media and publications](http://img-fotki.yandex.ru/get/6520/98991937.f/0_88d85_c556a5f_orig) Media and publications**

* [Pry article in Linux Journal](http://www.linuxjournaldigital.com/linuxjournal/201207?pg=34#pg29)

**![Blog Posts](http://img-fotki.yandex.ru/get/6621/98991937.f/0_88d84_6d76af6d_orig) Blog Posts**

* [“Turning IRB on its head with Pry”](http://banisterfiend.wordpress.com/2011/01/27/turning-irb-on-its-head-with-pry/) (by John Mair)
* [“The Pry Ecosystem”](http://banisterfiend.wordpress.com/2012/02/14/the-pry-ecosystem/) (by John Mair)
* [“Give it a Pry”](http://www.jonathan-jackson.net/give-it-a-pry) (by Jonathan Jackson)
* [“Pry 102: Advanced Features”](http://jonathan-jackson.net/2012/05/03/pry-session-102) (by Jonathan Jackson)
* [“Tossing out IRB for Pry”](http://www.philaquilina.com/2012/05/17/tossing-out-irb-for-pry/) (by Phil Aquilina)
* [“Pry Everywhere”](http://lucapette.me/pry-everywhere/) (by Luca Pette)
* [“Pry Ruby Open”](http://cubiclemuses.com/cm/articles/2011/12/19/pry-ruby-open/) (by William Taysom)
* [“Interactive debugging with Pry”](http://www.alanmacdougall.com/blog/2012/06/08/interactive-debugging-with-pry/) (by Alan Macdougall)
* [“Pry - A Rubyists best friend”](http://demiazz.github.com/blog/2012/06/10/pry-luchshii-drugh-rubista/) (in Russian, by Alexey Plutalov)
* [“Debugging With Pry”](http://yorickpeterse.com/articles/debugging-with-pry) (by Yorick Peterse)
* ["My Top 5 Pry Features"](http://www.bignerdranch.com/blog/my-top-5-pry-features/)(by Gregg Rothmeier)
* [“Pry: A new generation of debug tool”](http://blog.xdite.net/posts/2012/08/12/pry-the-new-debugger/) (in Chinese, by xdite)
* [“So what is "binding.pry" exactly?”](http://kyrylo.org/2013/05/30/so-what-is-binding-pry-exactly) (by Kyrylo Silin)
* [“binding.pry”って実際のところは何なのだろう](http://qiita.com/yui-knk/items/63a511d243323bec6fb8) (by yui-knk) (Japanese translated of “So what is "binding.pry" exactly?”)

**![Further](http://img-fotki.yandex.ru/get/6420/98991937.f/0_88d88_e95b3d0f_orig) Further Resources**

* [[FAQ]]
* [[Available Plugins]]
* [[Contributing]]
* [Pry website](http://pry.github.com)
* [Mailing List](https://groups.google.com/forum/#!forum/pry-rb)
* [API docs (YARD)](http://rdoc.info/github/pry/pry/master/file/README.markdown)
* [The README file](http://rdoc.info/github/pry/pry/master/file/README.markdown)
* [Pry with Padrino](https://github.com/johnknott/pry-padrino)

**![The Pry Core team](http://img-fotki.yandex.ru/get/6622/98991937.f/0_88d83_84540a07_orig) The Pry Core team:**

* [kyrylo](https://github.com/kyrylo/)

**![Reserve](http://img-fotki.yandex.ru/get/6522/98991937.f/0_88d82_58bf4fea_orig) Reserve**

* [banisterfiend](http://github.com/banister)
* [cirwin](https://github.com/conradirwin)
* [ryanf](https://github.com/rwfitzge/)
* [r-obert](https://github.com/r-obert/)