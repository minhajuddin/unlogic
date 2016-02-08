---
comments: true
date: 2015-05-14T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: http://i.imgur.com/dzF8q1B.png
modified: 2015-05-14 12:37:39 +0100
share: null
tags:
- pidgin
- passwords
- security
- logins
title: 'I made this: Purplescraper'
url: /2015/05/14/i-made-this-purplescraper/
---

Had this script sitting around for a while and I figured I would clean it up
a bit and share it. 

Get [Purplescraper from my Github](https://github.com/Svenito/purplescraper)

In short: you give it a starting directory, which will usually be where all the user
directories are, and it will go get all `.purple/accounts.xml` files and extract
any usernames and passwords it finds into a new file.

Useful to make sure none of your sensitive data is available to other, non
authorised users via slack file permissions.


