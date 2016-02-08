---
comments: true
date: 2012-10-11T00:00:00Z
published: true
tags:
- python
- programming
title: Parsing email in python
url: /2012/10/11/parsing-email-in-python/
---

I have a domain where I host images. It's nothing fancy, just a collection where gifs and generally humerous images get stored in a custom gallery script.
I also have a script on my host that I can pass a URL to and it will get the image with wget, put it in the right directory and then curl the import URL for the gallery. So if you've ever wanted to parse email addresses for content via a script, read on. Example code inside.

<!--more-->

Now this in principle is great, but sometimes I need to add an image from my phone and the whole ssh thing becomes a bit cumbersome. Usually I'll see something in Reeder or some other iPhone app which allows me to email the URL. Perfect. So I wrote a quick script that would do the work for me, and set up an email address that passes any content to this script. The script in question is here:

{% gist 3872497 %}

I use the ``fileinput`` module to read the data from stdin and then join it all to a single text chunk. I'm not expecting too much data in the email, so this isn't a big issue. Then I extract the message via the ``email`` module and parse the payload in order to get the actual email body, discarding the headers and all the other things I don't need. Assuming people have good etiquette and have the correct signature separator (``-- \n``), I also strip off the signature. 
Once I have the body I extract the image URL(s) using regular expressions, and then pass the URL to my import script.

As you can see this only works with image URLs that are prefixed with ``http://``. The email address is a random collection of letters and numbers to reduce the likelyhood of just anyone emailling links.

