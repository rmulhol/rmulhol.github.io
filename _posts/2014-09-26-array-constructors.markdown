---
layout: post
title:  "Working With Arrays: Choose Your Constuctor Wisely"
date:   2014-09-26 
categories: Ruby
tags: arrays
--- 

One of the projects a friend and I have been pairing on lately is a [Candyland][candyland] game. The game itself is pretty straightforward; it's meant to be accessible for small children and there's literally no strategy involved. However, coding out a version of the game is more challenging - it's been a very pleasant but also intellectually stimulating experience.

To get started, we have been working on setting up our board. We need to be able to store a fixed sequence of 100 spaces, where each space has a color and some spaces have other markings as well (e.g. all pink spaces are also marked with either a candycane or a gumdrop, five non-pink spaces are labeled as 'sticky' to indicate that you lose a turn if you land on them, etc.)

We decided to tackle this task with an array, and our first step was to create a new array that itself contained 100 empty arrays. We generated it with the literal constructor:

{% highlight ruby %}
def make_100_spaces
  new_board = []
  100.times do { new_board << [] }
  new_board
end
{% endhighlight %}

But after we got everything up and running, we went back to refactor and thought about trying the Array.new constructor:

{% highlight ruby %}
def make_100_spaces
  Array.new(100, [])
end
{% endhighlight %}

This feels like good refactoring. We're cutting back on lines of code and reducing the number of operations, so the only question is whether it achieves the same result.

Well, it does not. When we ran the tests, everything was blowing up. A bunch of unexpected outcomes early on, then some `NoMethodError` messages referencing the `NilClass` toward the end.

How could this be? I fired up an irb session and tried both approaches manually. Sure enough, our original approach was working as expected, and the Array.new constructor was not. I decided it was time to do some reading.

Luckily, the answer was easily accessible via [the Ruby documentation][rdoc]. It turns out that the Array.new constructor (without a block) points every element that receives the default value at the *same* value. When our program later wanted to iterate through the array to give each element a color, it was adding a new color to the same value each time. We ended up with an array of 100 subarrays, where each subarray had an identical sequence of 100 colors.

The takeaway is not, however, just to stick to the literal constructor. The Array.new constructor can work just fine as written above above when you want the default value to point every element that receives it at the same thing ("natively immutable values"). And, if that's not what you want, you can pass the Array.new constructor a block:

{% highlight ruby %}
def make_100_spaces
  Array.new(100) { [] }
end
{% endhighlight %}

When our code was refactored with the approach above, all of the tests pass.

So, brief detour, but the refactoring continues onward, equipped with new knowledge on the importance of choosing your constructors wisely.

[candyland]: http://en.wikipedia.org/wiki/Candy_Land
[rdoc]: http://www.ruby-doc.org/core-2.1.3/Array.html