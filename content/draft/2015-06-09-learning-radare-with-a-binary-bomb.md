---
comments: true
date: 2015-06-09T00:00:00Z
draft: true
image:
  credit: null
  creditlink: null
  feature: null
modified: 2015-06-09 17:18:33 +0100
share: null
tags:
- radare
- reverse engineering
- reversing
- binary bomb
- debugging
- decompile
title: Learning radare with a binary bomb
url: /2015/06/09/learning-radare-with-a-binary-bomb/
---

It's binary time at the moment, so putting some web based stuff aside, I'm 
going to focus more on matters binary. Since many years I've been using gdb
for all my debugging needs, with great success. Enhacing it with [gdbinit](https://github.com/gdbinit/Gdbinit)
and now [peda](https://github.com/longld/peda), I've had a fair but of success
exploiting binaries from the likes of [Smash the Stack](http://smashthestack.oth),
and challenges from [Over the wire](http://overthewire.org). But now I have this
urge to try out [radare](http://radare.org), and what better way to learn a tool
than to get stuck in and use it? 

For this exercise I'll be attempting the binary bomb from *csapp.cs.cmu.edu*. If you 
want to have a go, you can download it [here](https://www.dropbox.com/s/qn2qci2uuf6nz8b/bomb?dl=0)

# stage 1
