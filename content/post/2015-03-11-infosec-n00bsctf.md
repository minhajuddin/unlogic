---
comments: true
date: 2015-03-11T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2015-03-13 21:45:16 +0000
share: null
tags:
- security
- ctf
- infosec
title: Solving Infosec n00bsCTF
addtoc: true
url: /2015/03/11/infosec-n00bsctf/
---

The InfoSec Institue is running a [n00bsCTF](http://ctf.infosecinstitute.com/index.php),
and I can't resist a CTF really. Plus this will be a nice warmup for Hackyeaster 2015.
So let's jump on it and get some flags...

I haven't managed to get all of them, but I will be updating this post when
I manage to solve some of the others.

## Level 01 ##

[http://ctf.infosecinstitute.com/levelone.php](http://ctf.infosecinstitute.com/levelone.php)

{{< figure src="http://i.imgur.com/ebnLRwp.png" >}}

May the source be with you, eh? Sounds to me like someone wants us to look at the
page source. Right click, select ``view page source`` and...

{{< highlight html >}}

<!-- infosec_flagis_welcome -->
<!DOCTYPE html>
<html lang="en">
  <head>
{{< / highlight >}}

And there it is, right at the top.

    infosec_flagis_welcome

## Level 02 ##

[http://ctf.infosecinstitute.com/leveltwo.php](http://ctf.infosecinstitute.com/leveltwo.php)

{{< figure src="http://i.imgur.com/TVGZFKU.png" >}}

A broken image you say? Let's download it and have a look. We notice it's very small,
only 45 bytes. So let's open it in a hex editor and examine its contents. I opened
it in vim (to use it as a hex editor enter ``:!xxd``),
and was instantly greeted with this string. Didn't even have to convert it to hex.

    aW5mb3NlY19mbGFnaXNfd2VhcmVqdXN0c3RhcnRpbmc=

Base64 encoded text. Let's run it through a decoder and see what we get....

    infosec_flagis_wearejuststarting

Level02's flag done.

## Level 03 ##

[http://ctf.infosecinstitute.com/levelthree.php](http://ctf.infosecinstitute.com/levelthree.php)

{{< figure src="http://i.imgur.com/lL8OkTO.png" >}}

We are presented with a QR code. So I grabbed my phone, scanned it, and was presented
with a series of dots and dashes. Morse code no doubt. Being lazy I didn't want to type
it into a decoder manually,
so I used [this tool](http://zxing.org/w/decode.jspx) to decode the QR code
and then copy/pasted the output into a [morse code translator](http://www.onlineconversion.com/morse_code.htm)
and ended up with:

    INFOSECFLAGISMORSING

## Level 04 ##

[http://ctf.infosecinstitute.com/levelfour.php](http://ctf.infosecinstitute.com/levelfour.php)

{{< figure src="http://i.imgur.com/yFiSrus.png" >}}

A picture and a tidbit of information. Hrmm... Ok, not much to go with here. Mousing
over the image pops up a dialog. Investigating that didn't lead me anywhere. Time
to get the thinking hats on and think about what info we have here that might be
relevant. Because everything we need to solve this level is somewhere here.
Of course the biggest clue is the picture: Cookie Monster. Right, let's check the cookie
cache. Only one cookie by infosecinstitute. It's garbage! Or is it perhaps just a
caesar cipher? Each letter is shifted by a certain amount, and we need to figure out
that amount. Due to the number of characters and _ in the string, it looks like it.

Knowing the format of previous flags we can figure out how much to shift by.

So `vasbfrp_syntvf_jrybirpbbxvrf` should be `infosec_flag_xxxxxxx`. Knowing this
we can work out the shift. Simple write out the alphabet once and
then, write the letters we know below.

    abcdefghijklmnopqrstuvwxyz
    n                 f   i

That's enough for us to fill in the rest of the alphabet.
Then transpose and solve the final part to get:

    infosec_flag_welovecookies

Flag has been nommed.

## Level 05 ##

[http://ctf.infosecinstitute.com/levelfive.php](http://ctf.infosecinstitute.com/levelfive.php)

A pesky popup saying I'm a hacker? What an accusation. Well, let's top it from
popping up and get ourselves a script blocker to see what else there might be.
Once done, we examine the source and see it's loading an image too.

{{< highlight html >}}
<img src="img/aliens.jpg" /> <br /> <br />
{{< / highlight >}}

{{< figure src="http://i.imgur.com/JwFtmSw.png" >}}

Let's load up that image, or even better, let's just browse to `http://ctf.infosecinstitute.com/img/`
(Since writing directory listing has been disabled for this path).
Nothing particularily odd about the image. Let's try seeing if there's anything hidden in
it by way of steganography. I loaded up the image into [this site](http://www.futureboy.us/stegano/decinput.html)
and sure enough, selecting a type of `text/plain` we get a stream of 1s and 0s. Binary data.
Let's try to convert that to a string and see what, if anything, it says. Using any
binary to text decoder on the internet, we are given the flag:

    infosec_flagis_stegaliens

Gotcha

## Level 06 ##

[http://ctf.infosecinstitute.com/levelsix.php](http://ctf.infosecinstitute.com/levelsix.php)

{{< figure src="http://i.imgur.com/w81ZV0N.png" >}}

At first I was a bit lost, but when you stare at a lot of data, it's easy to be overwhelmed.
Especially when you don't know really what you are looking for. I decided to give it another go
and have located the flag. It's actually in the very first packet. The hex string for *infosec_flag*
is starting to look very familiar now. It appears as the data of the first packet:

{{< figure src="http://i.imgur.com/aO8ojXG.png" >}}

Enter the string with ``696e66...`` into a hex to string converter and you get

    infosec_flagis_sniffed

Sniffed right out.

## Level 07 ##

[http://ctf.infosecinstitute.com/404.php](http://ctf.infosecinstitute.com/404.php)

{{< figure src="http://i.imgur.com/PZu5CIK.png" >}}

We get an error saying *f00 not found* and the URL reads 404.php. Going by the other URLs
I would assume we actually need ``levelseven.php``, so let's enter that and see what we get.
It seems to return an empty page. That's not much use, but it's better than a real
404. Because there's no error and no content, we must be getting back a 200 status (all OK).
Perhaps there's something else to look at too?

In this case let's examine what's going back and forth between us and the server.
You can either fire up a proxy like [ZAP](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)
or use a request inspection plugin for Firefox like [httpRequester](https://addons.mozilla.org/en-US/firefox/addon/httprequester/).

So let's send off a GET request for ``levelseven.php`` and see what we get back.

{{< highlight html >}}
HTTP/1.0 200 aW5mb3NlY19mbGFnaXNfeW91Zm91bmRpdA==
Date: Thu, 12 Mar 2015 09:26:48 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.6
Content-Length: 0
Connection: close
Content-Type: text/html
{{< / highlight >}}

Heh, very nice. We have a 200 response with, what is clearly a base64 encoded string.
Copypasta that into a decoder and let's see what we get:

    infosec_flagis_youfoundit

Yes, I did find it.

## Level 08 ##

[http://ctf.infosecinstitute.com/leveleight.php](http://ctf.infosecinstitute.com/leveleight.php)

{{< figure src="http://i.imgur.com/OSKPz0g.png" >}}

Ok, let's download ``app.exe`` and give this a go. It's a wrapper around ``netstat`` that
just shows you what your current connections are. It takes no arguments, so there's
not anyway to attack this thing with overflows. And this being a n00bs level CTF, it's
unlikely we'll be thrown this kind of exploit development.
So the other thing we can do is check its strings.

{{< highlight console >}}
strings app.exe
{{< / highlight >}}

Sure enough, there's ``infosec_flagis_0x1a``. At first I was reluctant to go with this, as
it's a bit too simple, so I looked a bit further. I dumped the source with ``objdump``

{{< highlight console >}}
objdump -s app.exe | less
{{< / highlight >}}

And I found ``infosec_flagis_0x1a`` in it. It sits at ``0x403000``, so let's run this
through ``gdb`` too.

{{< highlight console >}}
$> gdb app.exe
gdb$ disass main
Dump of assembler code for function main:
   0x00401290 <+0>:	push   %ebp
   0x00401291 <+1>:	mov    %esp,%ebp
   0x00401293 <+3>:	sub    $0x18,%esp
   0x00401296 <+6>:	and    $0xfffffff0,%esp
   0x00401299 <+9>:	mov    $0x0,%eax
   0x0040129e <+14>:	add    $0xf,%eax
   0x004012a1 <+17>:	add    $0xf,%eax
   0x004012a4 <+20>:	shr    $0x4,%eax
   0x004012a7 <+23>:	shl    $0x4,%eax
   0x004012aa <+26>:	mov    %eax,-0x8(%ebp)
   0x004012ad <+29>:	mov    -0x8(%ebp),%eax
   0x004012b0 <+32>:	call   0x401740 <_alloca>
   0x004012b5 <+37>:	call   0x4013e0 <__main>
   0x004012ba <+42>:	mov    $0x403000,%eax       <------ aha oho
   0x004012bf <+47>:	mov    %al,-0x1(%ebp)
   0x004012c2 <+50>:	movl   $0x403014,(%esp)
   0x004012c9 <+57>:	call   0x401850 <printf>
   0x004012ce <+62>:	movl   $0x403044,(%esp)
   0x004012d5 <+69>:	call   0x401850 <printf>
   0x004012da <+74>:	movl   $0x403014,(%esp)
   0x004012e1 <+81>:	call   0x401850 <printf>
   0x004012e6 <+86>:	movl   $0x403072,(%esp)
   0x004012ed <+93>:	call   0x401840 <system>
   0x004012f2 <+98>:	call   0x4017c0 <getch>
   0x004012f7 <+103>:	mov    $0x0,%eax
   0x004012fc <+108>:	leave  
   0x004012fd <+109>:	ret
   0x004012fe <+110>:	nop
   0x004012ff <+111>:	nop
End of assembler dump
{{< / highlight >}}

There we see a fimilar address. Checking its contents

{{< highlight console >}}
gdb$ x/s 0x403000
0x403000:	 "infosec_flagis_0x1a"
{{< / highlight >}}

There's nothing that would indicate that this string changes, so for now, I'm
going to say the flag is:

    infosec_flagis_0x1a

Leave a comment below to correct me if I am wrong though, I'd really appreciate that.

## Level 09 ##

[http://ctf.infosecinstitute.com/levelnine.php](http://ctf.infosecinstitute.com/levelnine.php)

{{< figure src="http://i.imgur.com/5XnlOL9.png" >}}

Initially I struggled with this. I tried the usual default passwords without success.
Attacked it with SQL injections, nothing. Then I had a brainwave. Because I already had level15,
I could just look at the ``levelnine.php`` file. So I did. Issue ``test.com && cat ../levelnine.php``
and at the end we see what happens if we get the flag:

{{< highlight javascript >}}
alert('ssaptluafed_sigalf_cesofni')";
    }
{{< / highlight >}}

It's the flag reversed, so let's flip it to get:

    infosec_flagis_defaultpass

ti tog yllaniF

PS: What's even cuter is if you run ``test.com && tac ../levelnine.php``. You actually
get the popup. ``tac`` does the same as ``cat``, but reverses the lines in the output.
The reason this works is because it will encounter the javascript pop up code before
the conditional that checks the input.

It also prints out the username and password for us:
{{< highlight php >}}
?> } echo ""; if ($username == 'root' and $password == 'attack') { 
    $password = $_POST['password']; 
    $username = $_POST['username'];
{{< / highlight >}}

Now you can go to the levelnine url and enter that to get the flag. Either way should
be valid, as the aim of the game is to get the flag. It shouldn't matter how you get it :)

Level15 has been a huge help in all of this!

## Level 10 ##

[http://ctf.infosecinstitute.com/levelten.php](http://ctf.infosecinstitute.com/levelten.php)

{{< figure src="http://i.imgur.com/QENqipl.png" >}}

Let's listen to the sound. Hrmm Squeaky. Could be anything. Maybe some weirdly pitched morse.
But let's download and open it in Audacity. My initial hunch is that it's sped up, due
to the highpitched sound. So I started reducing the playback speed, and it turns out that
at around 0.15 times the original speed, we hear someone talking. He's actually telling us
the name of the flag

    infosec_flagis_sound

Thanks kind stranger

## Level 11 ##

[http://ctf.infosecinstitute.com/leveleleven.php](http://ctf.infosecinstitute.com/leveleleven.php)

{{< figure src="http://i.imgur.com/L2tFy5t.png" >}}

Hrmm.. there's no sound this time, instead we get the PHP logo. Well, it's all
we have so let's open it up. I loaded it into vim, changed to hex mode (:%!xxd)
and right at the top we have

    infosec_flagis_aHR0cDovL3d3dy5yb2xsZXJza2kuY28udWsvaW1hZ2VzYi9wb3dlcnNsaWRlX2xvZ29fbGFyZ2UuZ2lm

Yeah, that won't be it will it? We're very familiar with base64 now aren't we? Ok, decode

    http://www.rollerski.co.uk/imagesb/powerslide_logo_large.gif

Open it up and we get an image. Because the domain is outside of the control of
this CTF, we can assume that the image hasn't been tampered with or anything
is embedded in it and that the actual flag is:

    infosec_flagis_powerslide

I'll take it, thanks!

## Level 12 ##

[http://ctf.infosecinstitute.com/leveltwelve.php](http://ctf.infosecinstitute.com/leveltwelve.php)

{{< figure src="http://i.imgur.com/WdLl96v.png" >}}

Ok we recognise this image. We had it in level 1 and we had to look at the source.
Our clue is dig deeper and that could mean a number of things. Let's try some directory
traversal by appending a slash and some random text at the end. Hrm, we just
get a list of the levels and no css. Ok, that's not it. Let's dig into the source again.
There's nothing obvious, but I reckon it might be one of the files included in the source.
It would make sense given the clue we've been given.

To cut a long story short, there's a css file ``design.css`` that's not included
in the other pages. I know this because I've pretty much looked at the source for each
level. Force of habit. Anyway, let's take a look at it:

{{< highlight css >}}
.thisloveis{
	color: #696e666f7365635f666c616769735f686579696d6e6f7461636f6c6f72;
}
{{< / highlight >}}

Aha, that's not a valid colour is it? It's also not base64. Looks like hex values
to me. Run it through a hex to text converter and.....

    infosec_flagis_heyimnotacolor

Yeah, you weren't a colour, that's for sure.

## Level 13 ##

[http://ctf.infosecinstitute.com/levelthirteen.php](http://ctf.infosecinstitute.com/levelthirteen.php)

{{< figure src="http://i.imgur.com/JX3Je1g.png" >}}

Ok, this was a bit of a cheat, because I skipped to level 15 and now I have a a few more
tools available. I ran ``test.com && ls -la ..`` and voila, there's the backup file:
``levelthirteen.php.old``. Much easier than guessing, right?
Looking at this file we see it will prompt us to download a file called ``misc/imadecoy``.
This seems to be a network capture involving a project called [HoneyPy](https://github.com/shipcod3/honeypy).

I opened it up in wireshark and spent quite a bit of time on it. There's really nothing
interesting in it for the most part. However near the end we get a PNG image called
``HoneyPY.PNG``. For some reason this just stood out to me. I took a chance and exported it
by rightclicking the packet and selecting ``Export Selected Packet Bytes`` as shown

{{< figure src="http://i.imgur.com/jhH2v19.png" >}}

Opening this image surprises us with a flag! W00t! That flag is:

    infosec_flagis_morepackets

A wireshark level I managed to do. Yay

## Level 14 ##

[http://ctf.infosecinstitute.com/levelfourteen.php](http://ctf.infosecinstitute.com/levelfourteen.php)

{{< figure src="http://i.imgur.com/0akB0Ni.png" >}}

UPDATE: The ``level14.db`` file has been removed and the ``misc`` directory can
no longer be listed. Use the second method below to solve this level

Once downloaded we get a database backup file. This one was quite interesting because
there are two places where the flag is. So first I looked at what else is in the
``misc`` directory where this file lives. There's a ``level14.db`` file. Let's
take a look at that.

It adds an entry to the flag db. It's a hex string, so like above, simply decode it and
get

    infosec_flagis_whatsorceryisthis

But, look at the ``level14`` file again. Scroll through and notice that in the
`friends` table there's a fimilar entry. Decoding that will also give you the flag.

## Level 15 ##

[http://ctf.infosecinstitute.com/levelfifteen/index.php](http://ctf.infosecinstitute.com/levelfifteen/index.php)

{{< figure src="http://i.imgur.com/EVJuDmC.png" >}}

So here we can lookup a dns entry by typing a domain in the text field. So let's try
that and see what we get. I used ``test.com`` and as a result I get the output
from the ``dig`` command. Interesting. It could be that the php script is merely
calling ``dig`` with the search term appended to it. This is a bad way to execute
a command, and we will see why. In Linux you can append a command to another using ``&&``.
So let's try listing the directory with ``test.com&&ls`` as our search query. Sure enough,
we see ``index.php`` at the bottom of the output. Well, perhaps there's some hidden files so
let's run ``test.com && ls -la``. Aha, there's a file called ``.hey``, let's cat that with
``test.com && cat .hey`` and we get ``Miux+mT6Kkcx+IhyMjTFnxT6KjAa+i6ZLibC``

Not sure however where to go from here. The ZlibC at the end of the string might
be a clue.

Please leave any comments with ideas on this. I'm a bit stumped.

UPDATE: Turns out it's Atom 128 adn you can use [this site](http://crypo.in.ua/tools/eng_atom128c.php)
to decode it to `infosec_flagis_rceatomized`

Thanks to Anon and @fr1t3 for the info

ALL FLAGS DONE

## The lost level ##

UPDATED: This has since been removed, but I've left this here for posterity.

Not sure where this belongs, but in the misc folder is a file called ``readme.wav``
which contains the morsecode for

    INFOSECFLAGISMORSECODETONES

Which level this belongs to, I don't know.
