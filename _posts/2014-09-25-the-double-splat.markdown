---
layout: post
title:  "The Double Splat: Reduce Argument Order Dependency AND Accept an Indefinite Number of Arguments"
date:   2014-09-25 
categories: Ruby
tags: dependencies design
---

Reading through Sandi Metz's [*Practical Object-Oriented Design in Ruby*][poodr] has been awesome. She offers a bunch of helpful tips, and they're supported by examples that are (deceptively?) easy to understand. I plan to revisit and re-read it several times as I keep on learning.

One of the things that really jumped out at me on the first read is her advice for reducing argument order dependencies. That advice is [available online][metz_aod] and has been extensively [summarized][aod1] by [others][aod2], so I won't spend a ton of time unpacking  all of the details. But suffice it to say that you're basically replacing a hard-coded sequence of parameters with a single one - a hash - and then initializing your instance variables by calling the values associated with various keys.

Alright, I can't help myself. An example - this code is dependent on argument order:

{% highlight ruby %}  
class Box
  attr_reader :length, :width, :height

  def initialize(length, width, height)
    @length = length
    @width = width
    @height = height
  end
end
{% endhighlight %}

This code is not:

{% highlight ruby %}
class Box
  attr_reader :length, :width, :height

  def initialize(args)
    @length = args.fetch(:length)
    @width = args.fetch(:width)
    @height = args.fetch(:height)
  end
end
{% endhighlight %}

You can enter the arguments in any order in the latter example (labeling each with the appropriate symbol) when initializing a new instance, but the former requires arguments to be entered in the exact order of first length, then width, then height.

Reducing argument order dependency is nice, because it makes your code less error prone and more maintainable. You don't have to worry about getting bad data from entering arguments in the incorrect order, and you can can clearly see what key/value pairs are matched together where new instances are initialized.

Say we add a method that creates the area for the base of our box:

{% highlight ruby %}
def base_area
  length * width
end
{% endhighlight %}

This code will run correct every time if there's no dependency on argument order, but incorrectly if that order exists and you get it wrong when you initialize a new instance.

So that's cool, but what if you also want to reduce dependency on the number of arguments? Well, as an initial step, you could pass in some default values:

{% highlight ruby %}
class Box
  def initialize(args)
    @length = args.fetch(:length, 5)
    @width = args.fetch(:width, 4)
    @height = args.fetch(:height, 3)
  end
end
{% endhighlight %}

Now you can initialize a new instance where you only pass in one or two arguments, and those that you don't assign will be given the default value.

But you still have to pass in at least one argument, or else:

{% highlight ruby %}
=> wrong number of arguments (0 for 1) (ArgumentError)
{% endhighlight %}

The immediate solution that jumped to my mind here was to use a splat (*), since I'm used to that allowing me to enter an indefinite number of arguments. But that raised an unexpected result:

{% highlight ruby %}
=> no implicit conversion of Symbol into Integer (TypeError)
{% endhighlight %}

So, what's the deal? Well, the magic behind splat (that lets you enter an indefinite number of arguments) is that it's taking the arguments and generating an array:

{% highlight ruby %}
def see_how_splats_work(*args)
  puts args.inspect
end

> see_how_splats_work()
=> []
> see_how_splats_work(1)
=> [1]
> see_how_splats_work([1])
=> [[1]]
> see_how_splats_work([1], [2])
=> [[1], [2]]
{% endhighlight %}

Enter no arguments, and you get an empty array. That's not a problem in and of itself, since we've set a default value in our fetch calls. But the problem has to do with indexing. When you call fetch on an array, the argument you pass in must be an integer (the index for whatever position you're looking to fetch). When you call fetch on a hash, the argument you pass in is a key. We're passing a symbol into fetch and calling it on args, but the splat in our parameter means args is an array. Hence, the type error - Ruby wants an integer for an array index, not a symbol.

But, as with so many things, Ruby has a solution to this problem: the double splat. This tool lets you take any number of key-value pairs. Rewrite our earlier program like this:

{% highlight ruby %}
class Box
  attr_reader :length, :width, :height

  def initialize(**args)
    @length = args.fetch(:length, 5)
    @width = args.fetch(:width, 4)
    @height = args.fetch(:height, 3)
  end
end
{% endhighlight %}

And everything runs just fine, even if you don't pass any arguments.

The double splat lets you reduce argument order dependency in the fashion described by Metz while *also* reducing dependency on the number of arguments. Sweet.

[poodr]: http://www.poodr.com/
[metz_aod]: http://www.informit.com/articles/article.aspx?p=1946176&seqNum=2
[aod1]: http://www.technicalecstasy.net/removing-argument-order-dependency-in-ruby/
[aod2]: http://codequizzes.wordpress.com/2013/10/06/argument-order-dependencies/
