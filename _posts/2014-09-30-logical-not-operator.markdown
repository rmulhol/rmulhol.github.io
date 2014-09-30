---
layout: post
title:  "Checking A Negative: Using the Logical NOT Operator (!)"
date:   2014-09-30 
categories: operators
--- 

One of the tools that Ruby offers for evaluating objects is the logical NOT operator (the exclamation point, as in `x != y`). While the exclamation point is also used in other contexts to indicate that a method modifies the object it's called on in place (e.g. `gets.chomp!`), the focus of this post is on the use of the exclamation point as a tool for comparison.

Ruby offers [numerous operators][operators] for accomplishing all sorts of tasks, logical comparison being just one of them. Learning how and when to use these operators can be challenging at first (don't forget: `=` is for assignment, but you must use `==` for comparison!), but it quickly becomes second nature. 

As you get more familiar, the logical operators can be especially helpful. Say you want to take one array and return a new array that excludes all of a given element. You could easily do so like this:

{% highlight ruby %}
my_array.select { |element| element != element_to_exclude }
{% endhighlight %}

Run the code, and an array containing only the elements that do not equal the element you want to exclude will be returned.

So that's great. But wouldn't it be really useful if you could use the logical NOT operator for things other than equivalence? Say, for example, that you want to remove all elements in an array that do not include a given feature. For the inverse, you would obviously write:

{% highlight ruby %}
my_array.select { |element| element.include? feature }
{% endhighlight %}

But to actually check that a feature is not included, this doesn't work:

{% highlight ruby %}
my_array.select { |element| element.!include? feature_to_exclude }
{% endhighlight %}

So is it hopeless? Does the absence of a `.does_not_include?` method doom us?

Not at all. You *can* use the logical NOT operator for this sort of comparison, but its placement differs from how it's used in the `!=` comparison. To check that every element in an array does not include something, you simply write:

{% highlight ruby %}
my_array.select { |element| !element.include? feature_to_exclude }
{% endhighlight %}

By placing the logical NOT operator before the check for inclusion, you're basically saying "I only want to keep elements where it is NOT true that the element includes the feature I want to exclude." In this context, it makes sense that the logical NOT operator comes before the whole operation. You're not exactly running a method that checks that something is not included; you're running the method that checks that something *is* included but *also* saying you do NOT want those things.

So, a fairly minor tidbit, but one that opens up even greater utility for the ever useful logical NOT operator.

[operators]: http://www.tutorialspoint.com/ruby/ruby_operators.htm