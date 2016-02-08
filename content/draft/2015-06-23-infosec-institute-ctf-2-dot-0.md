---
comments: true
date: 2015-06-23T00:00:00Z
draft: true
image:
  credit: null
  creditlink: null
  feature: null
modified: 2015-06-23 22:35:55 +0100
share: null
tags:
- ctf
- infosec
- infosecinstitute
- solution
- writeup
title: Infosec Institute CTF 2.0
url: /2015/06/23/infosec-institute-ctf-2-dot-0/
---

=Level 01=

[Level 01](http://ctf.infosecinstitute.com/ctf2/exercises/ex1.php) features
good old fanshioned XSS. But there's a filter. Entering a typical XSS
payload gives us an error:

I need to modify the source and remove the check. Using *Firebug* I 
can view and edit the code. There's a filter on the `Site Name` field which
I need to edit. I changed the following

and entered `<script>alert("Ex1");</script>` to get the first flag.

=Level 02=

=Level 03=

=Level 04=

=Level 05=

=Level 06=

[Level 06](http://ctf.infosecinstitute.com/ctf2/exercises/ex6.php) is the classic
CSRF, I can make a simple URL with the required data, but then I'm told
that it should load when the person visits. All I need it to do is issue a `GET`
request to `http://site.com/bank.php?transferTo=555`. Let's see if any of the 
available tags are any use. 

I went with `img`. So using the following payload

{{< highlight html >}}
<img src='site.com/bank.php?transferTo=555'>
{{< / highlight >}}

the page will load that URL every page load. Lovely.

=Level 07=

=Level 08=

=Level 09=

[Level 09](http://ctf.infosecinstitute.com/ctf2/exercises/ex9.php) requires me to
pretend to be someone else. The page knows that I am `John Doe`, so how does
it do that? I'll check the cookies. Sure enough a new cookie is set that 
contains some random characters. Except they aren't random, they are base64, something
you get to recognise with practice. Using `echo ffffff | base64 -d` I see it represents
*JOHN+DOE*. Therefore I need to encode *MARY+JANE* to base64. With `echo MARY+JANE | base64` 
I get the data I need. Replacing the data in the cookie doesn't work though. I
missed one little detail: the *JOHN+DOE* string has no newline. `echo` has an option
to not print a new line. Sure enough `echo -n MARY+JANE | base64` gives the 
right string to put in the cookie. Reload the page and the flag is mine!

=Level 10=

=Level 11=

[Level 11](http://ctf.infosecinstitute.com/ctf2/exercises/ex11.php) requires
me to bypass a blacklist. But I didn't do anything. I'm a good, law abiding citizen.
Oh well, let's check the cookies again. Oh hello, there's a `welcome` cookie.
It's says I'm not welcome with a big, bold **no**. Well let me see if I change
their mind and edit the value to **yes**. Reload the page and victory. I'm a nice
person once again, but this time I have the flag.

=Level 12=



