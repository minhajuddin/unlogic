---
comments: true
date: 2012-06-06T00:00:00Z
published: true
tags:
- PyQt
- python
title: Sitting is bad
url: /2012/06/06/sitting-is-bad/
---

So the internet has recently been telling me that sitting is bad. Really bad. Well only if you [sit](http://www.msnbc.msn.com/id/34956099/ns/health-fitness/t/you-sitting-down-experts-say-itll-kill-you/) [for](http://mashable.com/2011/05/09/sitting-down-infographic/) [long](http://lifehacker.com/5800720/the-sitting-is-killing-you-infographic-illustrates-the-stress-of-prolonged-sitting-importance-of-getting-up>) [periods](http://www.sciencedaily.com/releases/2011/07/110712093859.htm) of time. At my office it is very unlikely that they will be willing to furnish me with a standing desk unless my doctor tells them to. The latter also being unlikely as my back isn't in a bad enough shape (yet).

I'm also not willing to [make my own](http://gregschlom.com/post/4555981908/standing-desk) version of a standing desk either, unless it was a better looking one :) So now what? Fear not. It turns out that if you stand for about 5 minutes every hour it already helps **A LOT**. So that I don't miss my standing breaks, I need a timer. An unobtrusive little app that sits in my taskbar and just alerts me at intervals to take a break and have a stand. Far be it for me to actually go looking for something that does this just how I want it. That sounds like too much work really. Thus I decided to turn it into a project for myself and write my own. I mean, how hard can it be?

Turns out: not so hard at all. After about an hour of hacking around with some PyQt I made this: [https://github.com/Svenito/EasyTimer](https://github.com/Svenito/EasyTimer)

It runs in the system tray, lets you set a custom timer period and then it pops up a window once the time expires. Not tested to destruction or particularily pretty, but it does what it needs to do. I'll need to tweak some dialogs and add some little features, but if you want to do yourself a favour and take regular breaks, grab the timer and give it a go.

Right, time for a stand me thinks....
