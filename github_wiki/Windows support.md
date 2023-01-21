Pry does work on windows, with a few minor caveats.

## MRI Issues

(MRI is the default Ruby implementation from [ruby-lang.org](https://www.ruby-lang.org/en/))

### `cmd.exe` doesn't handle output as utf-8

This means that if you try and output utf-8 from inside pry, you'll often see random characters instead.

To fix this, you need to change the code-page of the terminal while pry is running. You can add the following to your `\Home\<Username>\.pryrc` file:

```ruby
# Use utf-8 (codepage 65001) while pry is running so that you can output utf-8 strings correctly.
# Store the current codepage (e.g. 437) to be restored when pry exits to avoid permanent damage.
previous_codepage = `chcp`.scan(/[0-9]+/).first
system "chcp", "65001"
at_exit{ system "chcp", previous_codepage }
```

### `cmd.exe` cannot render utf-8

The default font in the terminal doesn't handle utf-8 anyway, so even if you've followed the above steps you'll be seeing lots of squares and random characters. This can be fixed by changing the font:

1. Open `cmd.exe`
2. Click on the window icon in the title bar
3. Go to "Properties" -> "Fonts"
4. Choose anything other than "Raster Fonts" (i.e. Lucidia Console, or Consolas)

### Readline doesn't respect input encoding

Even once you've set up output as described above, you'll notice that if you create strings they're still ANSI-8BIT by default, and not utf-8. If you'd like it to be utf-8 by default, you can add another hack to your `\Home\<Username>\.pryrc`:

```ruby
Pry.hooks.add_hook :after_read, :hack_utf8 do |str, _|
  str.force_encoding('utf-8')
end
```

This is basically the same as adding a ruby magic encoding comment to code you type in pry (`# encoding: utf-8`). If you prefer typing in another codepage (i.e you're not using the `chcp` trick above) feel free to force_encoding to the encoding of your choice.

### (unsolved) Cannot type/paste unicode characters into `cmd.exe`

If you try and type or paste a character into `cmd.exe` that is not supported by the current code-page then it will just not appear. This is made particularly bad by the `chcp 65001` hack above, as the keyboard/clipboard does not know how to translate to utf-8, so once you've done that you can only type 7-bit ascii into pry.

This is not the end of the world, as you can always use unicode strings inside ruby; but it is annoying.

```ruby
[1] pry(main)> puts "\u00f7";
"÷"
```

## Comprehensive MRI Fix

Except for the unsolved problem of typing/pasting non-ANSI characters into the terminal, the following class resolves many problems with pry integration into a Windows application. Requires the gems `win32console` and `windows-pr` in addition to `pry`.

```ruby
require 'windows/api'
require 'windows/console'
require 'win32console'
require 'pry'

module Helper
  class Cons
    @@before = false
    def self.setupPry()
      #Only run the meat of this method once, but allow the calling code to call it every time they `pry()`.
      if @@before
        return nil
      end
      @@before = true

      #Color commands don't work in a Windows console buffer.
      Pry.config.color = false

      #This sequence should be omitted if you have a console-based application. If you have a GUI-based application being executed with `rubyw.exe`, or if you run `ruby.exe` while hiding the command line window, you **must** include the following two `begin/rescue` clauses.
      begin
          Win32::Console::Free()
      rescue => xx
        puts xx
      end
      begin
          Win32::Console::Alloc()
      rescue => yy
        puts yy
      end

      #These three lines are required if you have the above Console::Alloc() line to reopen stdin/stdout/stderr
      $stdout = STDOUT.reopen("CONOUT$", 'w')
      $stderr = STDERR.reopen('CONOUT$', 'w')
      $stdin = STDIN.reopen('CONIN$', 'r')
      
      #This should deal with the codepage problem.
      @@previous_codepage = `chcp`.scan(/[0-9]+/).first
      system "chcp", "65001"
      at_exit { system("chcp", previous_codepage) unless @@previous_codepage.nil? }
      Pry.hooks.add_hook :after_read, :hack_utf8 do |str, _|
        str.force_encoding('utf-8')
      end
      
      #This should deal with the font problem by switching the current buffer (only!) over to Lucida Console. Note that this setting does NOT persist to other consoles, so you can keep your default console font as raster if you want.
      v = Windows::API.new('SetCurrentConsoleFontEx', 'LBP', 'B')
      fd = Windows::Console::GetStdHandle.call(-11)
      #Do a google for SetCurrentConsoleFontEx on MSDN to get information on what the meaning of the below numbers is. Do not change the first number of 84. The rest can be changed to customize the size and appearance of the font.
      struck = [84, 5, 9, 14, 54, 700, 'Lucida Console'].pack('QLssLLa32')
      v.call(fd, false, struck)
      nil
    end
  end
end
```

## jruby Issues

### Cannot use ANSI codes under jruby

ANSI codes are the magic escape sequences that let us use colour and re-indent code for you inside pry. These should mostly work under vanilla `cmd.exe` with MRI; but unfortunately do not work with jruby. To get around this, you can install [ansicon](https://github.com/adoxa/ansicon)

1. Download the latest release from https://github.com/adoxa/ansicon/downloads
2. Extract it `C:\ansicon`
3. Open `cmd.exe`
4. `cd \ansicon\x86` (for a 32-bit computer) or `cd \ansicon\x64` (for a 64-bit computer)
5. Run `ansicon.exe -i` to install ansicon.

## Cygwin Alternative

A nice way to side-step all these problems is to use ruby under [Cygwin](http://cygwin.com/). This has the advantage of making your environment much more similar to the typical Linux/Mac environment that most ruby developers are using. This means that everything will just work a bit better.


