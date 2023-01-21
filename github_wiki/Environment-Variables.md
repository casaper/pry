##### `DISABLE_PRY`
Disables Pry. Calls to `binding.pry` will *not* pause execution.

##### `FAIL_PRY`
Fails Pry. Calls to `binding.pry` will raise an error. 

You may find it useful to set `FAIL_PRY` in your CI environments to catch stray `binding.pry`s early.