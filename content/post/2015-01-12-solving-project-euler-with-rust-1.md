---
comments: true
date: 2015-01-12T00:00:00Z
image:
  credit: null
  creditlink: null
  feature: null
modified: 2015-01-13 20:38:10 +0000
share: null
tags:
- rust
- project euler
- programming
- learning
title: Solving Project Euler with Rust 1
url: /2015/01/12/solving-project-euler-with-rust-1/
---

It's time to poke at [Rust](http://www.rust-lang.org/) a little bit. And what
better way to get acquainted with a new language than to solve
some problems with it? And seeing as there's not always a suitably
simple problem handy, I've picked some problems from [Project Euler](https://projecteuler.net)
to tackle. I am using Rust 1.0.0Alpha in this post.

The first problem is:

    Multiples of 3 and 5
    Problem 1
    If we list all the natural numbers below 10 that are multiples of 3 or 5, 
    we get 3, 5, 6 and 9. The sum of these multiples is 23.

    Find the sum of all the multiples of 3 or 5 below 1000.
    
Ok, so sounds fairly ok and I'm going to add another requirement: To prompt the 
user for the max number (in this case the 1000). Just a little extra exercise. I've
read the [Rust By Example](http://rustbyexample.com/) pages and the new and official
[Rust book](http://doc.rust-lang.org/1.0.0-alpha/book) and let's see how we get on.

First let's define some structure:

{{< highlight rust >}}

fn solve(max_num: i32) -> i32 {
    let result = max_num;
    result
}

fn main() {
    println!("Total sum: {}", solve(10));
}
{{< / highlight >}}

`cargo run` simply prints out `Total sum: 10`. That's what we expect. I use `let result = 10;`
because I will be putting the result into a variable. The `result` with the semicolon
omitted is our return value. So far, so good.

Let's try to sum up the relevant numbers with a hard coded max value of 10.

{{< highlight rust >}}

fn solve(max_num: i32) -> i32 {
    let numbers = range(1, max_num).filter(|i| *i % 3 == 0 || *i % 5 == 0);
    numbers.fold(0, |acc, x| acc + x)
}

fn main() {
    println!("Total sum: {}", solve(10));
}
{{< / highlight >}}

Looks fun, right? So what's going on? First we generate a range iterator of numbers
divisible by 3 and 5. Pay attention to the `*i` which we need to use because the
filter value `i` is of type `&i32` and without it Rust handily tells you the same:

    error: binary operation `%` cannot be applied to type `&i32`

Next we use `fold` to sum all the entries. The [docs](http://doc.rust-lang.org/1.0.0-alpha/core/iter/trait.IteratorExt.html#method.fold) 
explain fold like this:

    Performs a fold operation over the entire iterator, returning the eventual
    state at the end of the iteration.

In this case we can liken it to Python's `map` function if you like, but a little different.
The first argument is the initial value that gets assigned to `acc`. Then the result of `acc + x`
gets assigned to `acc` to each entry `x`. Ultimately it's a `sum` in Python world.

Functionally we're done. The problem is solved as far as the initial requirement are concerned.
But I want to add some user input, so let's go over that part next.

{{< highlight rust >}}

fn solve(max_num: i32) -> i32 {
    let numbers = range(1, max_num).filter(|i| *i % 3 == 0 || *i % 5 == 0);
    numbers.fold(0, |acc, x| acc + x)
}

fn main() {
    print!("Enter the max number: ");
    let input = std::io::stdin().read_line().ok().expect("Failed to read line");
    let input_num: Option<i32> = input.trim().parse();
    
    println!("Total sum: {}", solve(input_num));
}
{{< / highlight >}}

We read the user input and make sure it was read ok (this is explained in detail in the book)
then we convert the input to `i32`. The `trim` is required to remove the newline char
at the nd of the input and the `parse` does the conversion.

But if we try to run this we get the following error:

    error: mismatched types: expected `i32`, found `core::option::Option<i32>` 
    (expected i32, found enum core::option::Option)

Right, because it's still an `Option<i32>` type. Helpfully the book explains that we need to 
unwrap the `Option`, and the best way to do this is with `match`:

{{< highlight rust >}}

fn solve(max_num: i32) -> i32 {
    let numbers = range(1, max_num).filter(|i| *i % 3 == 0 || *i % 5 == 0);
    numbers.fold(0, |acc, x| acc + x)
}

fn main() {
    print!("Enter the max number: ");
    let input = std::io::stdin().read_line().ok().expect("Failed to read line");
    let input_num: Option<i32> = input.trim().parse();
    
    let num = match input_num {
        Some(num) => num,
        None      => {
            println!("Please input a number!");
            return;
        }
    };

    println!("Total sum: {}", solve(num));
}
{{< / highlight >}}

Let's run this and see what happens:

{{< highlight console >}}
ninja:euler_1 unlogic$ cargo run
   Compiling euler_1 v0.0.1 (file:///work/code/rust/euler/euler_1)
/work/code/rust/euler/euler_1/src/main.rs:2:19: 2:24 warning: use of unstable item: will be replaced by range notation, #[warn(unstable)] on by default
/work/code/rust/euler/euler_1/src/main.rs:2     let numbers = range(1, max_num).filter(|i| *i % 3 == 0 || *i % 5 == 0);
                                                              ^~~~~
/work/code/rust/euler/euler_1/src/main.rs:8:34: 8:45 warning: use of unstable item, #[warn(unstable)] on by default
/work/code/rust/euler/euler_1/src/main.rs:8     let input = std::io::stdin().read_line().ok().expect("Failed to read line");
                                                                             ^~~~~~~~~~~
/work/code/rust/euler/euler_1/src/main.rs:8:17: 8:31 warning: use of unstable item, #[warn(unstable)] on by default
/work/code/rust/euler/euler_1/src/main.rs:8     let input = std::io::stdin().read_line().ok().expect("Failed to read line");
                                                            ^~~~~~~~~~~~~~
/work/code/rust/euler/euler_1/src/main.rs:9:47: 9:54 warning: use of unstable item: this method was just created, #[warn(unstable)] on by default
/work/code/rust/euler/euler_1/src/main.rs:9     let input_num: Option<i32> = input.trim().parse();
                                                                                          ^~~~~~~
     Running `target/euler_1`
Enter the max number: 1000
Total sum: 233168
ninja:euler_1 unlogic$
{{< / highlight >}}

Some warnings about unstable calls, but it's an Alpha release, so what else can we expect? But the end
result is there.

Well that was a nice little trip into Rust land, wasn't it?

UPDATE: I posted this on [reddit](https://www.reddit.com/r/rust/comments/2s9lam/just_started_playing_with_rust_heres_a_write_up/) 
and having taken some suggestions on board, I have made some small changes:

{{< highlight rust >}}
use std::iter::AdditiveIterator;

fn solve(max_num: i32) -> i32 {
    let mut numbers = (1..max_num).filter(|i| *i % 3 == 0 || *i % 5 == 0);
    numbers.sum()
}

fn main() {
    print!("Enter the max number: ");
    let input = std::io::stdin().read_line().ok().expect("Failed to read line");
    let input_num: Option<i32> = input.trim().parse();
            
    let num = match input_num {
        Some(num) => num,
        None      => {
            println!("Please input a number!");
            return;
        }
    };

    println!("Total sum: {}", solve(num));
}
{{< / highlight >}}

Also made the code (and all future solutions) available on [Github](https://github.com/Svenito/euler_rust)
