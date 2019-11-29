---
layout: post
title: TTY Spawn Shell Cheat Sheet 
description: This is a collection of one-liners commands that will allow you to spawn TTY shells!
---
 
## very useful for escaping jail shells. 
```perl
python -c 'import pty; pty.spawn("/bin/sh")'
```
```perl
echo os.system('/bin/bash')
```
```perl
/bin/sh -i
```
```perl
perl —e 'exec "/bin/sh";'
```
```perl
perl: exec "/bin/sh";
```
```perl
ruby: exec "/bin/sh"
```
```perl
lua: os.execute('/bin/sh')
```
```perl
exec "/bin/sh"
```
From within vi
```perl
:!bash
```
From within vi too
```perl
:set shell=/bin/bash:shell
```
From within nmap
```perl
!sh
```


