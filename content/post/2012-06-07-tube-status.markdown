---
comments: true
date: 2012-06-07T00:00:00Z
published: true
tags:
- python
- CLI
- Conky
title: Tube status for Conky
url: /2012/06/07/tube-status/
---

Last night I was affected by "severe delays on the Central line" caused by a burst watermain and bringing part of the line to its knees. Unfortunately I only found this out once I got to the station. Usually the line is well behaved and I have little need to check it often. I know it's really quick to do so and there's a whole host of ways to dit too. But the problems I have with them are:

1. **Check online**: Have to remember to go online and check, which I often/always forget.
2. **iPhone app**: Same again. Have to remember to actually do that each time I leave the office.
3. **Email alerts**: I get a fair bit of email each day and end up just clicking *delete* or *mark as read* on stuff like that

So here's what I've come up with 

<!--more-->

A CLI app that checks the tube status for any number of given lines. "Yeah, how does that help?" I hear you ask. "You still have to use it to check it" you say. True. But there's another way to use it: Conky.

I use [Conky](http://conky.sourceforge.net/) and I love it. So what better than to have the tube status for the relevant lines **always** on my desktop and updating every few seconds? So I quickly put together this python script to accomplish the task:

{% gist 2888187 %}

And it looks like this:

{{< figure src="center /images/content/conky.png" >}}

To add it to Conky just edit the ``.conkyrc`` file and add a line like so: ``$alignr${execi 2 /path/to/tube.py central -s}`` where *central* is the name(s)  of the line(s) (separate multiple lines with spaces) you want to get statuses for. The full list of lines is [here](http://tubeupdates.com/documentation/). The -s simply tells the script to supress the output of the full message and shows only the line name and short status.

It'd be good to know if you find it useful or if you have any suggestions for it.
