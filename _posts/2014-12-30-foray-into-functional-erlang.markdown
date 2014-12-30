---
layout: post
title:  "My Foray into Functional Programming: Getting Started with Erlang"
date:   2014-12-30
categories: Erlang
tags: functional
--- 

One of the things that I've been working on lately is wrapping my head around [functional programming][func_prog]. Having completed most of my projects so far in Ruby and Java (languages that support the [object-oriented programming paradigm][oo_prog]), beginning to work with a language that encourages the functional programming style felt like a logical next step. Enter Erlang.

Erlang is technically an "impure" functional programming language; its methods can have side effects. However, working in Erlang has  introduced me to many of the features that separate functional programming from its object oriented companion. No longer can I reassign variables or tackle problems with my trusty `for` loop. Nay, new approaches must now be uncovered.

So, now that I've begun to wrap my head around some of the ways that common problems can be approached using the functional programming style, I wanted to share some of the obstacles I've come across along the way as well as how they were overcome. 

**Referential Transparency, or: How I Learned to Stop Worrying and Love Single Assignment**

One of the main things that separates functional from object-oriented programming is immutable data. Once a variable has been assigned a value, that's it - you can't change it later.

This property is rooted in the concept of [referential transparency][ref_trans]; the idea is that when you pass the same argument to the same function, you should always get the same result. Or, [to quote Uncle Bob][func_ep_1], "it means that you can replace any function call with the value it returns."

Variable re-assignment runs counter to refential transparency since the value that a variable references can change. If your function were to rely on a mutable variable, then you could pass in the same argument to the same function and get different return values on each call depending on the current value of that variable.

Referential transparency sounds nice in theory. It certainly helps align my thinking about programming with the way I thought about functions in math class (high school algebra would have been *a lot* more difficult if the same variable in an expression could have had multiple values), but it also introduces a new set of challenges.

Coming from the world of object-oriented programming, I had grown so used to passing around important values in instance variables and using all sort of loops and iterators to analyze data that operating without variable re-assignment was nigh unthinkable.

Still, functional programming has been around long enough - and used to do enough - that I knew there had to be an answer, so I kept plugging away. And, as is wholly predictable, it is indeed possible to solve the same problems I had conquered with object-oriented programming, you just have to take a different path to get there.   

The classic example I've run across so far is generating sums. In Java, I could do that like this:

{% highlight java %}
public int sumNumbers(int num) {
  int sum = 0;
  for(int i = 0; i <= num; i++ ) {
    sum += i;
  }
  return sum;
}
{% endhighlight %}

Since we can't use a `for` loop that re-assigns `i` on each iteration, functional programming languages require us to find a different approach. In Erlang, that's pretty simple: 

{% highlight erlang %}
sum(Num) ->
  lists:sum(lists:seq(1, Num)).
{% endhighlight %}

Well, that's elegant. I've tackled the same problem without immutable data, and it actually made my code shorter.[^1]

So, what's going on here? Well, my sum function takes an argument, converts that argument into a list of all of the integers from 1 to that argument, and then provides a sum of all of the values in the list. It achieves the same result as my `for` loop without iteration or variable reassignment.

**Recursion: Did You Mean Recursion?**

While the above solution makes use of Erlang's built in functions (BIFs) to create and sum the elements of a list, another popular technique for solving this sort of problem in functional programming is to make use of [recursion][recursion]. In a nutshell, recursion refers to a function that calls itself repeatedly until a base case is reached, at which point a value is returned.

Recursion isn't unique to functional programming - I had already used it to make my minimax algorithm for the tic tac toe game I wrote in Java. And yet, recursion makes life a whole lot easier when trying to solve problems in functional programming that would typically, in object-oriented programming, make use of loops and iterators.

For example, a recursive function written in Erlang to find the sum of all integers up to a given value might look like this:

{% highlight erlang %}
sum(0) -> 0;
sum(Num) ->
  Num + sum(Num - 1).
{% endhighlight %}

Here, we make use of [pattern matching][p_match] to declare a function with two clauses. The first clause says, "when the argument passed in is 0, return 0". The second function clause says, "for any other value, return the sum of the argument with a recursive call to sum where the argument is decremented by 1."[^2] 

With this function declaration, a call to sum(3) would play out like this:

{% highlight erlang %}
sum(3)
3 + sum(2)
3 + 2 + sum(1)
3 + 2 + 1 + sum(0)
3 + 2 + 1 + 0
3 + 2 + 1 
3 + 3
6
{% endhighlight %}

The solution works, but, as you can see, it can also take up a lot of memory. Since each recursive call adds a new stack frame to the call stack, there's a risk that, when generating the sum for a very large number, your stack could get too deep.

One way to address this problem is to use [tail recursion][tail_rec].[^3] With tail recursion, you eliminate the need for adding a new stack frame for every call by storing the value up to that point as an argument. For example, we could generate a sum with this function:

{% highlight erlang %}
sum(Num) ->
  sum(Num, 0).

sum(0, Acc) -> Acc;
sum(Num, Acc) ->
  sum(Num - 1, Num + Acc).
{% endhighlight %}

Here, we use pattern matching for a few purposes. If we receive one argument, we call sum with two arguments: the original argument passed in and 0 (which will serve as our accumulator). On subsequent calls, we decrement `Num` by one and add the value of `Num` to our accumulator (`Acc`). Calling sum(3) with this function would play out like this: 

{% highlight erlang %}
sum(3)
sum(3, 0)
sum(2, 3)
sum(1, 5)
sum(0, 6)
6
{% endhighlight %}

Since each call can stand on its own and does not require us to maintain the previous return values up to that point, this solution achieves the same end without creating the same risks to our memory.

**Conclusion: Just Getting Started** 

Getting used to immutable data, referential transparency, recursion, and tail-call optimization has made working with Erlang a whole lot easier. I'm no longer at a loss for how to approach problems where I used to reach for variable reassignment, loops, and iterators. But there's clearly a lot left to learn, and I'm excited to dig deeper. I look forward to posting updates here as I progress with learning the tricks of functional programming.

**Notes**

[^1]: This is where some functional programming advocates begin to declare the elegance of functional programming over object-oriented programming. While I'm sure there's some truth to that in the aggregate, I still appreciate that Ruby comes pretty close: `(1..num).to_a.reduce(:+)`.
[^2]: This function will not play nicely with negative numbers, though that could be handled with further pattern matching (e.g. `sum(Num) when Num < 0 -> io:format("Can't sum negative values!~n", []);`).
[^3]: It should be noted that tail recursion [does not necessarily yield better performance][tail_myth] than body recursion, due to optimizations in the Erlang compiler.

[func_prog]: http://en.wikipedia.org/wiki/Functional_programming
[oo_prog]: http://en.wikipedia.org/wiki/Object-oriented_programming
[ref_trans]: http://en.wikipedia.org/wiki/Referential_transparency_(computer_science)
[func_ep_1]: http://blog.8thlight.com/uncle-bob/2012/12/22/FPBE1-Whats-it-all-about.html
[recursion]: http://en.wikipedia.org/wiki/Recursion_%28computer_science%29
[p_match]: http://learnyousomeerlang.com/syntax-in-functions
[tail_rec]: http://en.wikipedia.org/wiki/Tail_call
[tail_myth]: http://www.erlang.org/doc/efficiency_guide/myths.html#id59389