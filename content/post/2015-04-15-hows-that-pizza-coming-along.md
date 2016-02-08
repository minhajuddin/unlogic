---
comments: true
date: 2015-04-15T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2015-05-14 17:03:31 +0100
share: null
tags: []
title: How's that Pizza coming along?
url: /2015/04/15/hows-that-pizza-coming-along/
---

**UPDATE**: Published what I have done so far [on my Github](https://github.com/Svenito/dominos)

Having been a bit busy with other things recently, I've not mentioned the Domino's
thing for a while. So in case anyone is wondering where I am with this:

<script type="text/javascript" src="https://asciinema.org/a/17706.js" id="asciicast-17706" async></script>

Basically I've reworked how the store finder works. I've collapsed the whole store finding and 
delivery postcode stuff into one `locate_store` call. 
Enter your postcode and it'll get the nearest delivery capable store and select it. 
Much simpler, as you can see.

Now you also specify your name, phone number, and email when you select the 
delivery address. The payment side of things is coming along, but is only in debug
at the moment.

### What's planned ###

I want to actually place a *cash on delivery* order and eat a CLI pizza. Once that's finished, 
I will clean up the project, put it on Github, and package it for *pip*.

Then I'll get to work on investigating if I can handle card payments via this tool too. Certainly
there seems to be some relevant info available. With some luck this will work on some sort of
URL callback, but I'll find out eventually.

Then it's really about hoping that enough people like it, and want to help
by adding support for ordering in their own country.

