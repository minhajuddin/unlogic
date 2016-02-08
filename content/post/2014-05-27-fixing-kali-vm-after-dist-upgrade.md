---
comments: true
date: 2014-05-27T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2014-05-27 23:16:37 +0100
share: null
tags:
- kali
- virtualbox
title: Fixing Kali VM after dist-upgrade
url: /2014/05/27/fixing-kali-vm-after-dist-upgrade/
---

I just did a `apt-get dist-upgrade` to bump up to Kali 1.0.7.
I've got it running on VirtualBox, and after it booted back up all the
VBoxAddition features were gone and the keyboard and mouse in X no longer
worked. GRUB was fine however. Poking around the logs a little I discovered
some issues with the mouse driver. Perhaps that was it, but why?

Long story short it took quite a bit of digging, but I eventually managed 
to fix it. First thing is that the `pcscd` group just *dissapeared*. 
All the following commands assume you are root, otherwise
prefix them with `sudo`. So I ran

`groupadd pcscd` 

to add the group back in. Then run 

`apt-get install linux-headers-$(uname -r)`

This will take a little while. Once done run 

`/etc/init.d/vboxadd setup`

And it will build the kernel modules for the additons. One more reboot
and you should be back to a working system with all the integration you
had before.

I have no idea why this happens, but hopefully this post will help you
should you have the same problem.
