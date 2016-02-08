---
comments: true
date: 2015-01-23T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2015-01-23 09:34:49 +0000
share: null
tags:
- python
- programming
- wat
title: 'Python: expect the unexpected'
url: /2015/01/23/python-expect-the-unexpected/
---

I've not really had much of a play with Python 3, but I'm aware of some of its
differences. Yesterday I found out about a difference that took me by surprise.
Enough of a surprise that I felt the urge to write this post. 

Opinion Divided
===============

What surprised me was how `/` has changed in Python 3. In Python 2.7 it returns
the result of the division of two numbers.

{{< highlight python >}}
>>> 8/2
4
>>> 10/3
3
{{< / highlight >}}

Checks out to me. `10 / 3` is 3.3333, and because we are using integers in
the expression, we expect an integer as the result.
Change the input to floats (or at least one of the inputs)

{{< highlight python >}}
>>> 10/3.0
3.3333333333333335
{{< / highlight >}}

and we get a float. Right, nothing weird there. Where it starts getting odd is 
that in Python 3 you **always** get a float back, unless you use the `//` operator.
Apparently that is because too many people expected integer division to return a float.
Maybe it's just me and my fellow oldies who think that the original behaviour is
correct and integer division should yield an integer, not a float. Pretty much all
main stream languages behave like this. In C/C++ you need to cast one of the arguments
to a float to get a float back.

{{< highlight cpp >}}
#include <iostream>

int main () {
    std::cout << 10 / 3 << std::endl;
    std::cout << (float)10 / 3 << std::endl;

    return 0;
}
{{< / highlight >}}

{{< highlight console >}}
$] ./a.out 
3
3.33333
{{< / highlight >}}
 
I understand that if you are dividing numbers you will want to have 
the accuracy of the float type, but I find this a bit of an odd choice 
for the Python devs to make. But perhaps this is the future, and I'm
just too old to accept what you whippersnappers are up to with your
fancy [languages and tools](http://i.imgur.com/GUum4gy.gif).

Ultimately, does it really matter? Well yes and no. No, because Python is
dynamically typed, so it doesn't really matter what type the result is, whatever
it gets assigned too will become what it needs to. Yes, because there may be
times when getting a float might cause unexpected behaviour. 

It's not the end of the world as such, because the *no* above greatly 
outweighs the *yes*, but I'm still a little surprised at this change. I would 
perhaps have kept `/` as it is and made `//` the one that always returns a float.

And just to finish:

{{< highlight python >}}
Python 3.4.0
>>> 10//3
3
>>> 10//3.0
3.0
{{< / highlight >}}

[Yeah, sure, why not?](http://i.imgur.com/WEllYN3.gif)

