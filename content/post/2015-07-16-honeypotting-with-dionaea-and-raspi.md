---
comments: true
date: 2015-07-16T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2015-07-16 12:41:21 +0100
share: null
tags:
- security
- honeypot
- dionaea
- raspberry pi
- malware
title: Honeypotting with Dionaea and Raspi
url: /2015/07/16/honeypotting-with-dionaea-and-raspi/
---

I recently setup a [dionaea](http://dionaea.carnivore.it/) honeypot on my Raspberry Pi
and after tweaking and configuring it for a few days have now got a working setup.
It's a low interaction honeypot aimed to capture malware rather than ssh bruteforce 
attacks. 

I plan to leave it online for a week or a month, and the analyse the stats and see
what it managed to collect. So far it's mostly conficker variants, but there's a
suprising (to me) large number of infected machines out there. In one 8 hour period 
during testing, it managed to collect 8 unique samples from 154 connections.

Here's the stats from that period:

{{< figure src="http://i.imgur.com/j828Fpw.png" >}}

It should be fairly interesting to see if anything else comes along during its 
uptime.

