---
layout: post
title:  "Reductions in Clojure: Understanding the Coin Changer Kata"
date:   2015-05-06
categories: Clojure
tags: functional reductions 
--- 

One of the tasks I've been working on lately completing is the coin changer kata in Clojure. The objective is to calculate what coins you should distribute to most efficiently make a given amount of change. 

I'm blessed that there's [a video out there][coin_kata] of Micah Martin performing this exact task, but being able to perform the kata feels a little bit empty without having a sense for what's going on under the hood.

And while the nature of task lends itself pretty well to the understanding (in US currency, simply provide larger coins until their value exceeds the amount you still owe), the implementation in Clojure is a bit complicated:

{% highlight clojure linenos %}
(defn change-for [amount]
  (let [denominations [25 10 5 1]
        amounts (reductions #(rem %1 %2) amount denominations)
        coins (map #(int (/ %1 %2)) amounts denominations)]
    (mapcat #(take %1 (repeat %2)) coins denominations)))
{% endhighlight %}

So let's try and unpack what's going on here.

Lines 1 and 2 aren't too difficult - we define a function with one parameter and bind the vector `[25 10 5 1]` to our `denominations` variable. So far, so good.

But line 3 gets more tricky. What is this `reductions`?

Turns out, nothing all too complicated. 

If you're (like me) more familiar with Ruby than Clojure, then you might be familiar with `.inject` - a handy way to perform operations on collections.

Say you want to get the sum of an array. You might call:

{% highlight ruby %}
[1, 2, 3, 4, 5].inject(:+)
=> 15
{% endhighlight %}

If you want to begin your calculation of the sum from an initial value, you can do that too: 

{% highlight ruby %}
[1, 2, 3, 4, 5].inject(5, :+)
=> 20
{% endhighlight %}

As you may know, `.inject` also goes by the alias `.reduce` - these two methods accomplish the same task.

{% highlight ruby %}
[1, 2, 3, 4, 5].reduce(:+)
=> 15
[1, 2, 3, 4, 5].reduce(5, :+)
=> 20
{% endhighlight %}

Clojure also makes use of `reduce`:

{% highlight clojure %}
(reduce #(+ %1 %2) [1 2 3 4 5])
;= 15
{% endhighlight %}

And it can also take an initial value:

{% highlight clojure %}
(reduce #(+ %1 %2) 5 [1 2 3 4 5])
;= 20
{% endhighlight %}

So why the detour? Well, it turns out that `reductions` simply provides a sequence of the intermediate values involved in calling `reduce`:

{% highlight clojure %}
(reductions #(+ %1 %2) 5 [1 2 3 4 5])
;= (5 6 8 11 15 20)
{% endhighlight %}

Our sequence contains the initial value `5` followed by each of the values computed from adding on the next element in the collection (`5 + 1 = 6`, `6 + 2 = 8`, `8 + 3 = 11`, etc.).

This means that in the coin changer code above, `reductions` is giving us a collection containing the initial amount we owe followed by the amounts we still owe after factoring out larger denominations:

{% highlight clojure %}
(reductions #(rem %1 %2) 99 [25 10 5 1])
;= (99 24 4 4 0)
{% endhighlight %} 

We still owe 24 cents after factoring out quarters, 4 cents after factoring out dimes and nickels, and then we're done after we've distributed 4 pennies.

So, how does this fit into the rest of the solution?

Line 4 maps the quotients of dividing the `amounts` by the `denominations` (`99 / 25 = 3`, `24 / 10 = 2`, `4 / 5 = 0`, etc.): 

{% highlight clojure %}
(map #(int (/ %1 %2)) '(99 24 4 4 0) [25 10 5 1])
;= (3 2 0 4)
{% endhighlight %}

And line 5 concatenates the mapping of the number of each coin with its denominational value:

{% highlight clojure %}
(mapcat #(take %1 (repeat %2)) '(3 2 0 4) [25 10 5 1])
;= (25 25 25 10 10 1 1 1 1)
{% endhighlight %}

The solution solves the problem, it's elegant, and it makes sense.

[coin_kata]: https://vimeo.com/53519408