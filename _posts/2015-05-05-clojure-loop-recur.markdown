---
layout: post
title:  "Tail Call Optimization in Clojure: Using Loop/Recur"
date:   2015-05-05
categories: Clojure
tags: functional recursion
--- 

Recently, I've been getting into [Clojure][clojure]. To get started, I've begun working through the [Clojure Koans][koans]. They've provided a really helpful introduction to the language's power and syntax.

One thing that I got stuck on, however, was a problem involving recursion. The objective is to define a function capable of computing the factorial of a really big integer.

Computing a factorial is relatively straightforward:

{% highlight clojure linenos %}
(defn factorial [n]
  (if (zero? n)
    1
    (*' n (factorial (dec n)))))
{% endhighlight %}

This will work fine with smaller numbers:

{% highlight clojure %}
(factorial 5)
;= 120
{% endhighlight %}

But it runs into trouble with bigger numbers:

{% highlight clojure %}
(factorial 10000)
;= StackOverflowError   clojure.lang.Numbers.isZero (Numbers.java:90)
{% endhighlight %}

The trouble this method is running into is that each recursive call to `factorial` is adding a new frame to the stack, as the value of `n` at every call has to be retained in memory. 

A standard approach to optimizing this sort of function in functional programming would be to implement some sort of tail call optimization -- have the function store the value that it's reached up to its current point so that it doesn't have to retain every value it computed before then in memory:

{% highlight clojure linenos %}
(defn factorial [n acc]
  (if (zero? n)
    acc
    (factorial (dec n) (*' n acc))))
{% endhighlight %}

But that will not save us here:

{% highlight clojure %}
(factorial 10000 1)
;= StackOverflowError   clojure.lang.Numbers.isZero (Numbers.java:90)
{% endhighlight %}

Clojure, because it runs on the JVM, [does not support tail call optimization][absence].

So what's a lonely aspiring Clojure programmer to do?

The answer, it turns out, is [the `recur` construct][recur]. When placed at the end of a function, `recur` provides the benefits we'd expect from tail call optimization.

{% highlight clojure linenos %}
(defn factorial [n acc]
  (if (zero? n)
    acc
    (recur (dec n) (*' n acc))))
{% endhighlight %}

This method works!

{% highlight clojure %}
(factorial 10000 1)
;= 2846259680917054518906413212119868890148...(really big number)
{% endhighlight %}

One final hangup in the Clojure Koans is to retain the arity of the original function (`factorial` takes only one parameter). Since we need an accumulator to store the value we've computed up to the current call, we're going to need to bind some new values inside our function.

We can accomplish this with [the Clojure `loop` construct][loop]:

{% highlight clojure linenos %}
(defn factorial [n]
  (loop [new-n-for-looping n
         acc-for-recursion 1]
    (if (zero? new-n-for-looping)
      acc-for-recursion
      (recur (dec new-n-for-looping) (*' new-n-for-looping acc-for-recursion)))))
{% endhighlight %}

Here, we bind the passed value of `n` to our `new-n-for-looping` and `1` to our `acc-for-recursion`. The accumulator is multiplied by the current value of `new-n-for-looping` in each recursive call until the base case is reached (`new-n-for-looping` reaches zero), and we can compute the factorial of very large numbers without that pesky StackOverflowError.

[clojure]: http://clojure.org/
[koans]: http://clojurekoans.com/
[absence]: http://clojure.org/functional_programming#Functional%20Programming--Recursive%20Looping
[recur]: http://clojure.org/special_forms#Special%20Forms--%28recur%20exprs*%29
[loop]: https://clojuredocs.org/clojure.core/loop