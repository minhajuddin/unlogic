---
comments: true
date: 2012-08-13T00:00:00Z
published: true
tags:
- cmake
- gcov
- programming
title: cmake and gcov
url: /2012/08/13/cmake-and-gcov/
---

Recently I setup a project that uses CMake as its build tool and [googletest](https://code.google.com/p/googletest/) as a unit test framework. As is common place I wanted to make sure that my tests cover as much of the code as possible, so I went and grabbed the trusty gcov/lcov to analyse the tests only to find it wasn't as easy as I expected. I should mention this is the first time I have used CMake aswell as googletest. Granted, googletest is fairly simple and doesn't really complicate things when it comes to getting code coverage. I just had to figure out how we get CMake to build the test runner properly and then how to invoke lcov correctly. Turns out this was fairly easy too, once you ironed out some of the trickier bits and learned a little more about CMake.

In the ``CMakeLists.txt`` file for your test suite you need to add the following (I've omitted some lines that aren't relevant to this article):

{{< highlight cmake >}}
SET(PROJECT_TEST_NAME ${PROJECT_NAME_STR}_test)
    
SET(CMAKE_CXX_FLAGS "-g -O0 -Wall -fprofile-arcs -ftest-coverage")
SET(CMAKE_C_FLAGS "-g -O0 -Wall -W -fprofile-arcs -ftest-coverage")
SET(CMAKE_EXE_LINKER_FLAGS "-fprofile-arcs -ftest-coverage")

.   
.
.

target_link_libraries(${PROJECT_TEST_NAME} "gtest_main" "gtest" "pthread")
{{< / highlight >}}

We need to make sure a debug build is on (``-g``), that we build without optimisation (``-O0``), and enable profiling (``-fprofile-arcs -ftest-coverage``). On the link phase we need to link against the google unit test libraries and pthread. Once you have sucessfully built your unit test you can then use lcov to generate the coverage results. Although you'll soon notice that it might not work. CMake places its files into different directories than you'd expect from make or other build systems. So here's what I did to get this to work. Assuming you have a ``tests`` directory in your build, where your tests are and the test runner binary is built into, you have to run the following from that directory (``${PROJECTDIR}/build/tests``):

{{< highlight bash >}}
$ lcov --zerocounters --directory .
$ lcov --capture --initial --directory . --output-file app

# Now run your test app in the same directory

$ lcov --no-checksum --directory . --capture --output-file app.info
$ genthml app.info
{{< / highlight >}}

Now you can point your browser to that directory and you will have the nice html view of your coverage data.

*Note*: sometimes I got an error from the second to last line saying it could not find any gcno files. In this case I just ran the test runner again and then ran the last two lines from above again.

Hope this helps you out in case you have the same issue as me. 
