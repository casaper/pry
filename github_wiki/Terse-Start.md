Saying `require 'pry';binding.pry` is too much to type every time you want to drop into Pry from a script.

Here are some solutions.

# TODO soon

Use `binding_of_caller` to get it to something like: `load'pry/here'`

# Vim mappings

    " Add the pry debug line with \bp (or <Space>bp, if you did: map <Space> <Leader> )
    map <Leader>bp orequire'pry';binding.pry<esc>:w<cr>
    " Alias for one-handed operation:
    map <Leader><Leader>p <Leader>bp
    " …also, Insert Mode as bpry<space>
    iabbr bpry require'pry';binding.pry

    " Keep pry from annoyingly hanging around when using, e.g. pry-rescue/minitest
    map <f3> :wa<cr>:call system('kill-pry-rescue')<cr>

    " Nab lines from ~/.pry_history (respects "count")
    nmap <Leader>pry :<c-u>let pc = (v:count1 ? v:count1 : 1)<cr>:read !tail -<c-r>=pc<cr> ~/.pry_history<cr>:.-<c-r>=pc-1<cr>:norm <c-r>=pc<cr>==<cr>
    nmap <Leader>ph <Leader>pry
    " ↑ thanks to Houl, ZyX-i, and paradigm of #vim for all dogpiling on this one.

    nmap <Leader>rib of = File.open ENV['HOME']+'/ribson', 'w'; set_trace_func -> *args { f.puts [args, args[4].send(:local_variables) ].inspect }<esc>:w<cr>

# Shell Script + eval ?

    echo 'echo "require \"pry\"; binding.pry"' > ~/bin/ph; chmod +x ~/bin/ph

Then you're supposed to be able to `ph`, and it works except for a critical detail: when you do that the "binding" is wrong. Any ideas?

# Global method
```rb
def dbg(b_ing = binding)
  require 'pry'
  b_ing.pry
end
```

- `(+)` It spares you the require part
    - `(-)` But not the binding, which is the way you most likely would use it
- `(-)` Has to be defined on a per-project basis, though it:
    - `(+)` Lets you use any editor
    - `(+)` Lets you use any CLI command (like cucumber, rails, etc...)
