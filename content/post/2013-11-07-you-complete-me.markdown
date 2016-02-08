---
date: 2013-11-07T12:50:23Z
description: Getting YCM to work
tags:
- vim
title: YouCompleteMe
url: /2013/11/07/you-complete-me/
---

If you haven't heard of the YouCompleteMe plugin for Vim, headover to 
[http://valloric.github.io/YouCompleteMe/](http://valloric.github.io/YouCompleteMe/) and take a look.
It's a very competent auto completer for a variety of languages. But as always the C style completer takes
a little bit of work to get going. So just for you, I've written up how I managed to get it to work
on 64bit Centos 6.2.

<!--more-->

So using [Vundle](https://github.com/gmarik/vundle) install YouCompleteMe (referred to as YCM from now on).
Now we need to build clang. I managed to get this done by following [these steps](http://clang.llvm.org/get_started.html).
Use ``CC="/usr/bin/gcc" CXX="/usr/bin/g++" ../llvm/configure`` to configure it.

You will end up with a directory called ``build`` that contains almost everything. All you have to do is copy the 
``llvm/tools/clang/include/clang-c`` folder from the original checkout (step 2 if you follow the clang guide) to 
``build/include``.

Now we need to build the YCM tools according to the docs. Here's the command I used:

{{< highlight bash >}}
cmake -G "Unix Makefiles" -DPATH_TO_LLVM_ROOT=/tmp/build -DEXTERNAL_LIBCLANG_PATH=/tmp/build/Release+Asserts/lib/libclang.so . ~/.vim/bundle/YouCompleteMe/cpp
{{< / highlight >}}

adjust the paths as necessary. After the configure stage run

{{< highlight bash >}}
make ycm_support_libs
{{< / highlight >}}

And with some patience you are done.

Now you need to add a ``.ycm_extra_conf.py`` to your project and you should start seeing autocompletion.
    
