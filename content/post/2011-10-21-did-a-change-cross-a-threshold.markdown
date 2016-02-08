---
comments: true
date: 2011-10-21T15:43:01Z
published: true
tags:
- programming
title: Did a change cross a threshold?
url: /2011/10/21/did-a-change-cross-a-threshold/
---

So recently I needed to check if a change to a number caused it to cross a threshold. In this case, did the change cause a crossing of a threshold that is a multiple of 25? To try and make this a little easier to understand:

* True if the first value is 14 and the new value is 28. (crosses 25)
* True if the first value is 43 and the new value is 58 . (crosses 50)

You get the idea. So how do we best do it. With some thinking it's actually quite easy as you just base the decision off of the number of multiples each number is of 25:

{{< highlight python >}}
prev_multiple = t[1] / 25;
new_multiple = new_percentage / 25;

if (new_multiple > prev_multiple):
    print "Crossed"
{{< / highlight >}}

Nice and simple :)
