pssh
====

pssh is a Python3 library to run a number of SSH processes in parallel.  
As pssh uses [asyncio][], it requires Python 3.4 or newer.

Features
--------

* `Pssh` class to run SSH in parallel.
  * SSH outputs are forwarded to local stdout/stderr.  
    Every line is to be prefixed by the remote hostname.
  * Instead of forwarding, SSH outputs can be collected in memory for later use.
* Easy to integrate `Pssh` into your asyncio application.
* A file can be given as inputs for all SSH processes.
* Limit on the number of simultaneous SSH processes.
* Built-in command-line interface.

Command-line
------------

```
pssh.py [-i FILE] host1,host2,... COMMAND [arg1 arg2 ...]
```

### Invoke "date" at once.

```
$ ./pssh.py host-1,host-2,host-3 date
[host-3] Sat May 23 07:20:22 UTC 2015
[host-1] Sat May 23 07:20:22 UTC 2015
[host-2] Sat May 23 07:20:22 UTC 2015
```

### Send a file at once.

```
$ ./pssh.py -i $HOME/.bashrc host-1,host-2,host-3 dd of=$HOME/.bashrc
[host-2] 7+1 records in
[host-2] 7+1 records out
[host-2] 3650 bytes (3.6 kB) copied, 3.0315e-05 s, 120 MB/s
[host-3] 7+1 records in
[host-3] 7+1 records out
[host-3] 3650 bytes (3.6 kB) copied, 6.8925e-05 s, 53.0 MB/s
[host-1] 7+1 records in
[host-1] 7+1 records out
[host-1] 3650 bytes (3.6 kB) copied, 3.0475e-05 s, 120 MB/s
```

Python module
-------------

### Use Pssh.run() for non-asyncio application.

```
>>> import pssh
>>> p = pssh.Pssh(['host-1', 'host-2', 'host-3'], ['date'], use_stdout=True)
>>> p.run()
True
>>> from pprint import pprint
>>> pprint(p.outputs)
{'host-1': b'Sat May 23 07:30:07 UTC 2015\n',
 'host-2': b'Sat May 23 07:30:07 UTC 2015\n',
 'host-3': b'Sat May 23 07:30:07 UTC 2015\n'}
```

### Embed Pssh into your asyncio application.

```python
import asyncio, pssh

p = pssh.Pssh(['host-1', 'host-2'], 'date')
task = asyncio.async(p.wait())
task.add_done_callback(lambda x: ...)  # Use Pssh results.
```

License
-------

[MIT][]

Author
------

Yamamoto, Hirotaka ([@ymmt2005][])

[asyncio]: https://docs.python.org/3/library/asyncio.html
[MIT]: http://opensource.org/licenses/MIT
[@ymmt2005]: https://github.com/ymmt2005
