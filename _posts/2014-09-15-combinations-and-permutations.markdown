---
layout: post
title:  "Ruby: Combinations, Permutations, Repeated Combinations, Repeated Permutations"
date:   2014-09-15 
categories: Arrays
---

In [Apprenticeship Patterns][apprenticeship_patterns], Dave Hoover and Ade Oshineye encourage software apprentices to make breakable toys. Building programs for yourself and for fun, they propose, is a great way to grow, since you can gain experience stretching your skill set in a context where mistakes are of little consequence. 

This advice has really resonated with me, so I've jumped at opportunities to build my own breakable toys. I've been working for a little while on a [Mastermind][mastermind] game, and I recently got to try and develop a [Candy Land][candyland] game at a hack night. 

In building these games, one of the things I've found myself doing quite frequently is working with arrays of numbers that later get changed into something else. The code for my Mastermind game, for example, starts off with an array of numbers (say, [1, 1, 2, 2]), and then delivers that sequence to the user as a series of colors (["red", "red", "blue", "blue"]). The Candy Land board starts off as an array of numbers:

{% highlight ruby %}
board = []
100.times do 
  board << rand(6)
end
{% endhighlight %}

Then those numbers are converted into matching board spaces.

Working with these numbers and arrays has led me to stumble upon some Ruby syntax that I've found really helpful: combinations, permutations, repeated combinations, and repeated permutations. Though each of these methods are built into the Ruby language and bring a lot of utility, I've come across scant mention of them in the books and tutorials I've worked through so far. So, in this post, I want to lay out what these methods do and what you can accomplish when you put them to use.

Before delving into the specifics of each, it's worth mentioning that all of these methods are called on an array, take one argument, and can be invoked with a block. Without a block, the method returns an [Enumerator][enumerator] that can be converted to an array with a simple .to_a method. With a block, the method passes each of the arrays that would otherwise have been stored in the Enumerator to the block, and then returns the original array. For the sake of scope, I'll be limiting my explanation here to method calls that do *not* invoke a block and that *do* convert Enumerators into arrays.

Alright, down to the nitty gritty we go.

**Combinations**

The [combination][combination] method arranges elements together in a sequence where different arrangements of the same elements do not equate to a new combination. So, for example:

{% highlight ruby %}
> my_array = [1, 2, 3, 4]
> combination_array = my_array.combination(2).to_a

=> [[1, 2], [1, 3], [1, 4], [2, 3], [2, 4], [3, 4]]
{% endhighlight %}

Here we have all of the possible combinations of elements from my_array that are of length 2 (the argument we passed to our combination method call - we could just as easily have gotten all combinations of length 3 by passing that in as our argument).

Since our output includes [1, 2], it does not also include [2, 1]. Arranging 1 and 2 together in a different order does not equal a new combination.

Each element is also included only once per coupling; there is no (repeated) combination of [1, 1]. Since 1 is only present once in the original array, there is no other 1 with which it could be combined.

The combination method does not rule out that combinations might *appear* repeated or identical. You could, for example, get a [1, 1] combination and multiple [1, 2] and [1, 3] combinations simply by changing your initial array:

{% highlight ruby %}
> new_array = [1, 1, 2, 3]
> new_combination_array = new_array.combination(2).to_a
 
=> [[1, 1], [1, 2], [1, 3], [1, 2], [1, 3], [2, 3]]
{% endhighlight %}

But it is not repetition of a single element that produces this outcome. The two 1's at the beginning of my_array are treated as separate elements, and thus produce unique combinations when paired with each other, 2, or 3. 

**Permutations**

The [permutation][permutation] method differs from its combination comrade primarily in that arrangement *does* matter. [1, 2] is different from [2, 1], even if the 1 and 2 used in both permutations are the same in our original array:

{% highlight ruby %}
> my_array = [1, 2, 3, 4]
> permutation_array = my_array.permutation(2).to_a

=> [[1, 2], [1, 3], [1, 4], [2, 1], [2, 3], [2, 4], [3, 1], [3, 2], [3, 4], [4, 1], [4, 2], [4, 3]]
{% endhighlight %}

Permutations include [2, 1] alongside [1, 2], [3, 2] in the same sequence as [2, 3], and so on. Given the same initial array and argument, the permutation method thus generates a larger set of outputs than combination, since different arrangements of otherwise identical elements are treated as unique permutations.  
 
Like the combination method, however, permutation will not generate couplings that repeat one element - there is no [1, 1] in the permutation_array above.

**Repeated Combinations**

The [repeated combination][repeated_combination] method introduces the capability to pair an element from the initial array with itself, as many times as the argument you pass in the method call. And yet, as with its combination brethren, arrangement is not a distinguishing factor:

{% highlight ruby %}
> my_array = [1, 2, 3, 4]
> repeated_combination_array = my_array.repeated_combination(2).to_a

=> [[1, 1], [1, 2], [1, 3], [1, 4], [2, 2], [2, 3], [2, 4], [3, 3], [3, 4], [4, 4]]
{% endhighlight %}

[1, 1], [2, 2], [3, 3], and [4, 4] all repeat elements from our original array, but the output is devoid of permutations that simply rearrange existing combinations.

**Repeated Permutations**

The [repeated permutation][repeated_permutation] method, as you might by now have imagined, generates the largest possible set of outputs, allowing for each element to be repeated as many times as the argument it receives *and* treating different arrangements of already combined elements as unique. 

{% highlight ruby %}
> my_array = [1, 2, 3, 4]
repeated_permutation_array = my_array.repeated_permutation(2).to_a

=> [[1, 1], [1, 2], [1, 3], [1, 4], [2, 1], [2, 2], [2, 3], [2, 4], [3, 1], [3, 2], [3, 3], [3, 4], [4, 1], [4, 2], [4, 3], [4, 4]]
{% endhighlight %}

[1, 1], [1, 2], [2, 1], and [2, 2] are all included, even though there is only one 1 and one 2 in our initial array.

Run this:

{% highlight ruby %}
> my_array = [1, 2, 3, 4]
> my_array.repeated_permutation(3).to_a
{% endhighlight %}

And your output will include [1, 1, 2], [1, 2, 1], and [2, 1, 1].

**Utility: Why Does This Matter?**

Being able to elegantly generate outputs like this is tremendously useful when you're relying on arrays to build your breakable toys. With my Mastermind game, for example, I want the computer to be able to crack a code made up of 4 elements drawn from 6 colors, with repetitions allowed. To do so, I first have to generate all of the 1296 possible combinations ... er, repeated permutations ... and then pair them down according to user feedback. Generating that initial list could look like this:

{% highlight ruby %}
def generate_all_combinations
  possible_outcomes = []
  [1, 2, 3, 4, 5, 6].each do |n1|
    [1, 2, 3, 4, 5, 6].each do |n2|
      [1, 2, 3, 4, 5, 6].each do |n3|
        [1, 2, 3, 4, 5, 6].each do |n4|
          possible_outcomes.push([n1, n2, n3, n4])
        end
      end
    end
  end
  possible_outcomes
end
{% endhighlight %}

Or it could look like this:

{% highlight ruby %}
def generate_all_combinations
  [1, 2, 3, 4, 5, 6].repeated_permutation(4).to_a
end
{% endhighlight %}

I'm happy to be able to use the latter, and I know that the other methods contained herein can serve me well as I go on to tackle other tasks.

[apprenticeship_patterns]: http://chimera.labs.oreilly.com/books/1234000001813/index.html
[mastermind]: http://en.wikipedia.org/wiki/Mastermind_(board_game)
[candyland]: http://en.wikipedia.org/wiki/Candy_Land
[enumerator]: http://www.ruby-doc.org/core-2.1.2/Enumerator.html
[combination]: http://www.ruby-doc.org/core-2.1.2/Array.html#method-i-combination
[permutation]: http://www.ruby-doc.org/core-2.1.2/Array.html#method-i-permutation
[repeated_combination]: http://www.ruby-doc.org/core-2.1.2/Array.html#method-i-repeated_combination
[repeated_permutation]: http://www.ruby-doc.org/core-2.1.2/Array.html#method-i-repeated_permutation