---
comments: true
date: 2014-03-12T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2014-03-12 22:46:48 +0000
share: null
tags:
- coding
title: Effort Vs Benefit
url: /2014/03/12/effort-vs-benefit/
---

How often have you thought: "Everything would be a lot better if this 
tool worked faster"? Probably quite often. You've most likely also heard the
saying "Don't optimise prematurely". It's true, there's no point making
something faster if you don't know it's too slow. Don't waste time on 
trying to optimise before you get some metrics telling you it needs it.

What you might not have thought about though is whether the speed increase
is actually worth it. Sure, for something like game development where
every nanosecond matters it's very critical, but in other situations, 
it really depends on how the performance impacts the user's workflow.

A paper from 2011 titles [The Workflow Scale](https://www.dropbox.com/s/rqc3xpmtskxcxov/workflow.pdf) covers this subject very well. It is targeted at
a specific industry and workflow, but the same applies elsewhere.
It says that the benefit of a speed increase to a workflow depends on
how it changes the behaviour of the user. It's a short but well
written paper that every developer should read.

I appreciate that it doesn't apply to all cases, but it's information
that should be taken on board. The next time you decide you should spend
some time improving the speed of a certain task, consider if the payoff is
worth the time you will spend making the improvement.
