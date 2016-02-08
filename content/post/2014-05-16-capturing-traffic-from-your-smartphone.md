---
comments: true
date: 2014-05-16T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2014-05-16 15:57:15 +0100
published: true
share: true
tags:
- security
- network
title: Capturing smartphone traffic - Part 1
url: /2014/05/16/capturing-traffic-from-your-smartphone/
---

We all carry phones around that are connected to some of network at all times. 
Unless you are the truly paranoid or battery saving type, then there will be periods
when your phone is dark.

But even so, I often wonder what my phone is transmitting when I'm not using it,
or even when I am using it. So in this series I plan on doing some investigation
into what goes in and out of my phone. 

In this post I'll just set up all the bits and pieces to start the monitoring
process and run a quick check to make sure everything works. In the following
parts I'll be taking a look at some common apps and their network chatter. The 
idea is to analyse the data and understand their communications. Hopefully we'll
also unearth some interesting things along the way.

## What we'll need ##

* A smartphone
* A laptop (or similar) to turn into a wireless access point
* [Wireshark](https://www.wireshark.org/) running on that laptop
* An internet connection


## Setting up the wireless access point ##

I'm going to cover how to do this part in OSX for now. If I get round
to it I'll add a Linux way to do it too.

### OSX ###

First connect the laptop to an ethernet connection. You can't be connected to 
a wireless network and create a wireless access point at the same time.
So we will connect to the internet via ethernet and use the wireless radio
for the access point. Once the ethernet connection is up and running, open 
*System Preferences -> Sharing*. In the list click on the *Internet sharing* 
entry (not the check box) and select *Ethernet* in the *Share your connection
from* dropdown. In the *To computers using* select *Wi-Fi*. Now select the
check box next to *Internet Sharing* to turn sharing on.

[{{< figure src="http://i.imgur.com/K2Zeyoy.png" >}}](http://i.imgur.com/K2Zeyoy.png)

If everything went to plan your Wi-Fi indicator should now look like this:

[{{< figure src="http://i.imgur.com/ecXJUc8.png" >}}](http://i.imgur.com/ecXJUc8.png)

## Connect the phone ##

Now we connect tbe phone to the new access point. Go to your wireless network
settings and select the name of the network we created. **slaptop** in my case.
Once do we need to

## Start Wireshark ##

Fireup Wireshark and then select the capture interface. The default will be 
eth0 usually. That's the ethernet port, but we want to capture traffic on the
wireless access point. So we need to select *en1* (in my case). It's the one 
with the wireless icon next to it.

[{{< figure src="http://i.imgur.com/9wyI6s6.png" >}}](http://i.imgur.com/9wyI6s6.png)

Once this is done we'll want to filter out ARP packets as these are of little
interest and there will be quite a few of them. See the screenshot below to
see where to set this. 

Now we press the *Capture* button to begin bapturing traffic. Once running we
can check to see if it's working by browsing to a site. I chose this site at
`192.30.252.153`. You should see the capture window scroll past with some 
traffic going between your phone ip and the site your browsed to.

[{{< figure src="http://i.imgur.com/XjFuoOQ.png" >}}](http://i.imgur.com/XjFuoOQ.png)

## In the next post ##

So I'm all set to capture any and all traffic now that goes to and from my phone.
With this I can now open various apps and see what traffic they generate. In 
the next posts I plan to see what idle traffic there is (no apps being actively
used) and what traffic some common apps generate. If I'm lucky I might even 
discover some interesting things about some apps.


Stay tuned for part 2.
