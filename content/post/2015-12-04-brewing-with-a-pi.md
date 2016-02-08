---
comments: true
date: 2015-12-04T00:00:00Z
image: http://i.imgur.com/I6CQkev.jpg
modified: 2015-12-04 13:43:05 +0000
share: null
tags:
- beer
- brewing
- diy
- raspberry pyi
- python
- cherrypy
title: Brewing with a Pi
url: /2015/12/04/brewing-with-a-pi/
---

It's been a while since I've written here, but that doesn't mean I haven't
been busy. I've been working on a DIY version of [Speidel's Braumeister](http://www.speidels-braumeister.de/en/braumeister/id-10-20-50-litre-braumeister.html)
and the software side of things has come along quite nicely.

The idea is to have a Raspberry Pi controlled beer brewing system for cooking
the wort across a temperature profile. Much like the Braumeister, or Grainfather
style brewers. A probe monitors the temperature and will switch the heating on and 
off to maintain the current temperature. You can set how long to hold each temperature
for and it will just plod through the profile.

You can also set and hold a temperature if you want to simply heat the wort. A
pump running at intervals will also be operational during the brew. 

As I said, the software side is coming along, and I've done a quick test with a Pi
and the DS18B20 thermo probe, and that's also working. Next up I need to
get myself a dedicated Pi (perhaps the new Pi Zero) and the rest of the kit, including
vessels, piping, heating etc. It probably won't be fancy stainless steel like the
Braumeister, but it should achieve the same end result.
Once done I will write a complete build process, and the project will be open 
source so everyone can use and improve upon it.

But more about the software, it's running on [CherryPy](https://cherrypy.org) and 
currently looks like this:

[{{< figure src="http://i.imgur.com/c4IR4yT.png" >}}](http://i.imgur.com/c4IR4yT.png)
{{< figure src="http://i.imgur.com/c4IR4yT.png" title="Brewpy interface" >}}

I've put the [code on Github](https://github.com/Svenito/brewpy) already, so if
you are keen and fancy building the rest of the kit yourself already, please
check it out.

Hopefully I'll get the time to put the rest of the project together early
next year. Got a house to get up to speed as well, so time is a little limited.
