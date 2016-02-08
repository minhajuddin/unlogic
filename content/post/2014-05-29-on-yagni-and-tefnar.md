---
comments: true
date: 2014-05-29T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2014-05-29 09:26:31 +0100
share: true
tags:
- yagni
- tefnar
- programming
- principle
title: On YAGNI and TEFNAR
url: /2014/05/29/on-yagni-and-tefnar/
---

You've probably heard of [YAGNI](https://en.wikipedia.org/wiki/You_Ain%27t_Gonna_Need_It)
but not of TEFNAR. To recap YAGNI stands for **Y**ou **A**in't **G**onna **N**eed **I**t and
refers to the development principle of that you shouldn't write a feature that no one has asked for
in the first place as it's probably never going to be needed. Instead wait until
it's a requirement and implement it then. This saves wasted effort and keeps the code base
clean and as small as possible.

TEFNAR stands for **TE**chnology **F**or **N**o **A**pparent **R**eason and is a term
my boss coined (AFAIK). It basically refers to any unnecessary technology that
doesn't really add any functionality to the product. Sure it might look nice, but
is there a reason that's there or does that?

From this you can probably also see that YAGNI applies *before* and TEFNAR for *after*
the implementation. So with the background information done let me explain why I am writing
about this. A few people might not like the idea of YAGI and think it's better to
add a feature and make a useful app than omit it. Well allow me to give you an example
from the real world.

A fellow dev here implemented a feature that showed graphs to the user. He added a
feature to animate the graph points from 0 to their actual value each time you
were shown a graph. A very pretty feature indeed, but it doesn't add anything to
the graph. The animation doesn't represent another bit of data or a sort of
progress bar, it's just eye candy. I argued that although it's pretty people will
tire of it after a while. Much like you tire of most little animations you have to sit
through when you just want the end result. I also argued it's unnecessary complexity
that adds to the risk of introducing bugs and also adds another thing for future devs
to debug. Granted, it didn't take him long to code up, but stay tuned, here comes the
lesson.

Fast forward a day and I get called over

> Hey Sven, fancy a Python puzzle?

I took the bait and headed over.

> So in the interpreter `1.0 < 1.0` is `False`, which is correct.
> But now look at this \*runs small python script\*. See here, `1.0 < 1.0 True`
> How is that?

I look at the code and notice he's got a loop that adds 0.1 to a variable and
compares that to 1.0. I chuckled.

> Yeah, that's a different kettle of fish. Floats aren't that accurate that you
> can accumulate like that and expect it to be *exactly* 1.0. That's the issue.

He looked a little confused. I explained to him about how floats are represented.
If you are interested you can read up on [floating point values in Python](https://docs.python.org/2.7/tutorial/floatingpoint.html).

To demonstrate I opened an interpreter and ran this:

{{< highlight python >}}

>>> a = 0.1
>>> a += 0.1
>>> a += 0.1
>>> a += 0.1
>>> a += 0.1
>>> a
0.6
>>> a += 0.1
>>> a += 0.1
>>> a
0.7999999999999999

{{< / highlight >}}

I suggested he try the same by incrementing the value by `0.2` and the error went away.
He looked a little dejected. I asked what this was for and sure enough, it's how he
animated his graphs, so some points were slightly higher than they should be.

So be careful when you add code for no other reason than "it looks nice" as it's
just another area where you can introduce bugs. In this case it was a pretty small
issue, but a different feature could affect a larger part of the program or
even other programs.