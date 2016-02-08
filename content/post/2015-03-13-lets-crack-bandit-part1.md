---
comments: true
date: 2015-03-13T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: http://i.imgur.com/OHpNwbR.png
modified: 2015-03-13 21:29:29 +0000
share: null
addtoc: true
tags:
- ctf
- security
- hack
- overthewire
- bandit
title: Let's crack Bandit Part 1
url: /2015/03/13/lets-crack-bandit-part1/
---

Let's give Bandit from the [overthewire war games](http://overthewire.org/wargames)
a going over. I did this a while back, but never really wrote it up,
so I'm going to do it again and write it up. Remember that you can copy
and paste from all the [asciinema](https://asciinema.org) videos below.

Bandit is a CTF/wargame for beginners and a great intro to various
linux tools as well. I won't repeat the level summary for each post, instead
there's a link to the original page for each section.

I'd be interested to know if the asciinema files are preferable over the
plain text format or not. Let me know in the comments below. I've used
asciinema in the first level only, but would post the asciinema vids if they 
are useful.

<section id="table-of-contents" class="toc">
<header>
<h3>Contents</h3>
</header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

# Level 0 -> 1 #

[level 00](http://overthewire.org/wargames/bandit/bandit1.html)

Not much to do here but login and read a file so:

<script type="text/javascript" src="https://asciinema.org/a/17664.js" id="asciicast-17664" async></script>

# Level 1 -> 2 #

[level 01](http://overthewire.org/wargames/bandit/bandit2.html)

Using the password from the last session, let's login and look at what's in
``-``. The trick here is that ``-`` is a bit tricky to pass as an argument. Try
it to see what happens. All we need to do it prefix it with the path:

{{< highlight console >}}
bandit1@melinda:~$ ls -la
total 24
-rw-r-----   1 bandit2 bandit1   33 Jun  6  2013 -
drwxr-xr-x   2 root    root    4096 Jun  6  2013 .
drwxr-xr-x 160 root    root    4096 Oct 17  2013 ..
-rw-r--r--   1 root    root     220 Apr  3  2012 .bash_logout
-rw-r--r--   1 root    root    3486 Apr  3  2012 .bashrc
-rw-r--r--   1 root    root     675 Apr  3  2012 .profile
bandit1@melinda:~$ cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
{{< / highlight >}}

# Level 2 -> 3 #

[level 02](http://overthewire.org/wargames/bandit/bandit3.html)

Not much trickiness here, merely the spaces in the filename. But with TAB 
completion the escaping of the spaces will be handled for us:

{{< highlight console >}}
bandit2@melinda:~$ ls -la
total 24
drwxr-xr-x   2 root    root    4096 Jun  6  2013 .
drwxr-xr-x 160 root    root    4096 Oct 17  2013 ..
-rw-r--r--   1 root    root     220 Apr  3  2012 .bash_logout
-rw-r--r--   1 root    root    3486 Apr  3  2012 .bashrc
-rw-r--r--   1 root    root     675 Apr  3  2012 .profile
-rw-r-----   1 bandit3 bandit2   33 Jun  6  2013 spaces in this filename
bandit2@melinda:~$ cat ./spaces\ in\ this\ filename
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
{{< / highlight >}}

# Level 3 -> 4 #

[level 03](http://overthewire.org/wargames/bandit/bandit4.html)

Hidden file? Just do a long listing with ``ls -la``

{{< highlight console >}}
bandit3@melinda:~$ ls -la
total 24
drwxr-xr-x   3 root root 4096 Jun  6  2013 .
drwxr-xr-x 160 root root 4096 Oct 17  2013 ..
-rw-r--r--   1 root root  220 Apr  3  2012 .bash_logout
-rw-r--r--   1 root root 3486 Apr  3  2012 .bashrc
-rw-r--r--   1 root root  675 Apr  3  2012 .profile
drwxr-xr-x   2 root root 4096 Jun  6  2013 inhere
bandit3@melinda:~$ cd inhere/
bandit3@melinda:~/inhere$ ls
bandit3@melinda:~/inhere$ ls -la
total 12
drwxr-xr-x 2 root    root    4096 Jun  6  2013 .
drwxr-xr-x 3 root    root    4096 Jun  6  2013 ..
-rw-r----- 1 bandit4 bandit3   33 Jun  6  2013 .hidden
bandit3@melinda:~/inhere$ cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
{{< / highlight >}}

# Level 4 -> 5 #

[level 04](http://overthewire.org/wargames/bandit/bandit5.html)

We need to find a human readable file in the `inhere` directory. Using the 
power of bash:

{{< highlight console >}}
bandit4@melinda:~$ cd inhere/
bandit4@melinda:~/inhere$ for f in $(ls); do file ./${f}; done
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
bandit4@melinda:~/inhere$ cat ./-file07
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
{{< / highlight >}}

Change into the `inhere` directory and then for each file returned by the `ls` 
command, get the filetype with the `file` command. Only one which is ASCII, so
that's a good candidate. Sure enough, it's the one we are after.

# Level 5 -> 6 #

[level 05](http://overthewire.org/wargames/bandit/bandit6.html)

This is similar to the previous level, except now we are looking for something 
with a specific size. Luckily the `find` command is just right for this:

{{< highlight console >}}
bandit5@melinda:~$ find ./ -size 1033c
./inhere/maybehere07/.file2
bandit5@melinda:~$ file ./inhere/maybehere07/.file2
./inhere/maybehere07/.file2: ASCII text, with very long lines
bandit5@melinda:~$ cat !$
cat ./inhere/maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
{{< / highlight >}}

# Level 6 -> 7 #

[level 06](http://overthewire.org/wargames/bandit/bandit7.html)

Now we need to broaden our search. Once again `find` to the rescue. We know
the user and group that own the file and its size. The user and group might
be enough already, so let's give that a go

{{< highlight console >}}
bandit6@melinda:~$ cd /
bandit6@melinda:/$ find -user bandit7 -group bandit6  2> /dev/null 
./var/lib/dpkg/info/bandit7.password
bandit6@melinda:/$ cat ./var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
{{< / highlight >}}

Perfect. I piped the `stderr` to `/dev/null` so it doesn't clutter the output
with files that it can't read.

# Level 7 -> 8 #

[Level 07](http://overthewire.org/wargames/bandit/bandit8.html)

To find things in a file, `grep` is usually the answer. However it's probably
wise to check the file format first in case all the words are smushed together
and we need to filter grep again.

{{< highlight console >}}
bandit7@melinda:~$ head data.txt 
Kunming's	0D0KZ3TdLRBXD8lyd7Bj2hAqnxaMInQe
multitude's	8MFZa8yOjTt6m8PvxteTp7XTDFLiuFAk
audibility	ZeLj0yAw7ylmEoLxSUEqF4iB43c9DN4h
unadvised	Pgp8X2LSVdNrmIKcJ7Oe8eqTzEVfhGbR
Brecht's	uKyKryNUZYFuTQpwRlDqucLLIUbiIMF0
Alvin	IpQIV6mpjticdB790obqXAvYkAgnDV8E
insufficient	cgHhWVJahfDqFIe82vOliryQQ8ihGlGN
Sauterne	UhPBp0A04GkIRfvZnUt1UdwlKU2ViYUd
cluster	1GeFZ0B6rsEtJ5Sqb5h8Wv7UwG15DQzb
ember's	f2XPIE1iDHW9oHPyodPyfTz87DAbWmXu
bandit7@melinda:~$ grep millionth data.txt 
millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
{{< / highlight >}}

Luckily it was one word and password per line, so grepping the file worked
fine.

# Level 8 -> 9 #

[level 08](http://overthewire.org/wargames/bandit/bandit9.html)

So the only way we know which entry is the password is that it occurs
only once. For this the linux tool `uniq` seems perfect. However it can
only detect duplicate lines if they are next to each other. To fix this
we also need to sort the contents of the file and then display only
unique lines:

{{< highlight console >}}
bandit8@melinda:~$ cat data.txt | sort | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
{{< / highlight >}}

# Level 9 -> 10 #

[Level 09](http://overthewire.org/wargames/bandit/bandit10.html)

This `data.txt` file is in binary. So in order to find the strings we need
to dump it as hex, or, even simpler, run it through `strings`:

{{< highlight console >}}
bandit9@melinda:~$ strings data.txt  | grep ==
I========== the6
========== password
========== ism
========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
{{< / highlight >}}

# Level 10 -> 11 #

[Level 10](http://overthewire.org/wargames/bandit/bandit11.html)

Good ol base64. If you haven't seen it before, you'll get to see it a lot
more if you carry on doing these kind of challenges. Simply done though:

{{< highlight console >}}
bandit10@melinda:~$ cat data.txt  | base64 -d
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
{{< / highlight >}}

# Level 11 -> 12 #

[level 11](http://overthewire.org/wargames/bandit/bandit12.html)

The description is a basically a verbose way of saying that the string
has been encoded with rot13. The quickest way for me to un-rotate it, is
using python:

{{< highlight console >}}
bandit11@melinda:~$ cat data.txt 
Gur cnffjbeq vf 5Gr8L4qetPEsPk8htqjhRK8XSP6x2RHh
bandit11@melinda:~$ python -c 'import codecs;print codecs.decode("5Gr8L4qetPEsPk8htqjhRK8XSP6x2RHh", "rot13")'
5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
{{< / highlight >}}

# Level 12 -> 13 #

[Level 12](http://overthewire.org/wargames/bandit/bandit13.html)

From here on it's going to get a little trickier. We know that data.txt is a hexdump
of a binary, so first let's convert it back to a binary first with `xxd`

{{< highlight console >}}
bandit12@melinda:/tmp/unl$ cat data.txt | xxd -r > data2
{{< / highlight >}}

Then we can find out the filetype of data2

{{< highlight console >}}
bandit12@melinda:/tmp/unl$ file data2
data2: gzip compressed data, was "data2.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
{{< / highlight >}}

`gzip` it is. So uncompress that to data3

{{< highlight console >}}
bandit12@melinda:/tmp/unl$ cat data2 | zcat > data3
{{< / highlight >}}

and get its filetype next. I won't go over each step in detail as there's quite 
a few iterations. I'll post the console log of how I got to the flag and hopefully
that should be clear enough.

{{< highlight console >}}
bandit12@melinda:/tmp/unl$ file data3
data3: bzip2 compressed data, block size = 900k
bandit12@melinda:/tmp/unl$ bzcat data3 > data4
bandit12@melinda:/tmp/unl$ file data4
data4: gzip compressed data, was "data4.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
bandit12@melinda:/tmp/unl$ cat data4 | zcat > data5
bandit12@melinda:/tmp/unl$ file data5
data5: POSIX tar archive (GNU)
bandit12@melinda:/tmp/unl$ tar xf data5
bandit12@melinda:/tmp/unl$ ls
data.txt  data2  data2.bin  data3  data4  data5  data5.bin
bandit12@melinda:/tmp/unl$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@melinda:/tmp/unl$ tar xf data5.bin
bandit12@melinda:/tmp/unl$ ls
data.txt  data2  data2.bin  data3  data4  data5  data5.bin  data6.bin
bandit12@melinda:/tmp/unl$ file data6.bin 
data6.bin: bzip2 compressed data, block size = 900k
bandit12@melinda:/tmp/unl$ bzcat data6.bin > data7
bandit12@melinda:/tmp/unl$ file data7
data7: POSIX tar archive (GNU)
bandit12@melinda:/tmp/unl$ tar xf data7
bandit12@melinda:/tmp/unl$ ls
data.txt  data2  data2.bin  data3  data4  data5  data5.bin  data6.bin  data7  data8.bin
bandit12@melinda:/tmp/unl$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
bandit12@melinda:/tmp/unl$ cat data8.bin | zcat > data9
bandit12@melinda:/tmp/unl$ file data9
data9: ASCII text
bandit12@melinda:/tmp/unl$ cat data9 
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
{{< / highlight >}}

Basically we identify, extract, repeat, until we're at the plain text file with the
password.

# Level 13 -> 14 #

[Level 13](http://overthewire.org/wargames/bandit/bandit14.html)

We're given a lot of information here, and one of those is that we get the SSH
key for the `bandit14` user. We can use this to login as that user without knowing
the password:

{{< highlight console >}}
bandit13@melinda:~$ ssh -i ./sshkey.private bandit14@localhost 
Could not create directory '/home/bandit13/.ssh'.
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is 05:3a:1c:25:35:0a:ed:2f:cd:87:1c:f6:fe:69:e4:f6.
Are you sure you want to continue connecting (yes/no)? yes
.
.
bandit14@melinda:~$ cat /etc/bandit_pass/bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
{{< / highlight >}}

We pass the key as an argument to the ssh command, and connect to the localhost
as bandit14. Then we can read the file with the password.

# Level 14 -> 15 #

[Level 14](http://overthewire.org/wargames/bandit/bandit15.html)

This level starts introducing some networking and how to interact with remote
hosts. Well, in this case it's localhost, but the principle is the same.
We need to connect to a specific port on localhost and then supply
the current password. I'm using `netcat` to do this

{{< highlight console >}}
bandit14@melinda:~$ nc localhost 30000
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr
{{< / highlight >}}

All you get is a blank line when you've connected. The simply paste in the 
password you logged in with and hit enter.

Continues with [Let's crack Bandit Part 2](http://unlogic.co.uk/2015/03/13/lets-crack-bandit-part2)
