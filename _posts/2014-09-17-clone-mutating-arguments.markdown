---
layout: post
title:  "Clone: How to Stop Arguments from Mutating"
date:   2014-09-17 
categories: mutation
---

When you pass arguments to a method, you generally don't want that method to mutate the arguments you've passed in. You want the method to be able to use and evaluate the arguments so that it can return some data, but you don't want those arguments to be changed as a byproduct of that operation. And yet, in some instances, that's precisely what happens.

For example, say we want to write a method comparing two arrays. The goal is to identify how many elements the two arrays share in common. An initial implementation of this method might look like this:

{% highlight ruby %}
def we_got_something_in_common(array_one, array_two)
  in_common = 0
  array_one.each do |element|
    if array_two.include? element
      in_common += 1
      already_counted = array_two.index(element)
      array_two.delete_at(already_counted)
    end
  end
  in_common
end
{% endhighlight %}

If you run this method, it works: 

{% highlight ruby %}
> one_array = ["horses", "bunnies", "puppy dogs"]
> another_array = ["turtles", "unicorns", "puppy dogs"]
> we_got_something_in_common(one_array, another_array)
 => 1
{% endhighlight %}

But there's a side effect:

{% highlight ruby %}
> another_array
 => ["turtles", "unicorns"]
{% endhighlight %}

We lost our puppy dogs!! :(

So what's happening? Well, in the initial we_got_something_in_common method, we delete those elements from array_two that are already_counted. Even though another_array is just an argument that we're passing into the method to see how much it has in common with one_array, the former is mutated by this operation. But if we didn't include the delete_at statement in our method, it would return inaccurate results:

{% highlight ruby %}
def we_got_something_in_common(array_one, array_two)
  in_common = 0
  array_one.each do |element|
    if array_two.include? element
      in_common += 1
    end
  end
  in_common
end

> my_array = ["puppy dogs", "horses", "puppy dogs"]
> your_array = ["cats", "puppy dogs", "rabbits"]
> we_got_something_in_common(my_array, your_array)
 => 2
{% endhighlight %}

Without deleting the elements we've already counted, our .include? method returns true each time an element in the first array can also be found in the second. The two arrays above only have one thing in common - puppy dogs - but the first array happens to have two of them, so we get a result of two when we go through each element in the first array to check and see if it's included in the second.

Now, clearly there are lots of ways you could re-write your method so that you didn't need the delete_at that mutates the arguments you pass in. But if you like your original method and just want it to leave your arguments alone, a nice method to check out is .clone. With it, you can create a copy of the argument you're passing in so that changes to the copy don't change the original variable you passed in as an argument:

{% highlight ruby %}
def we_got_something_in_common(array_one, array_two)
  in_common = 0
  cloned_array = array_two.clone
  array_one.each do |element|
    if cloned_array.include? element
      in_common += 1
      already_counted = cloned_array.index(element)
      cloned_array.delete_at(already_counted)
    end
  end
  in_common
end
{% endhighlight %}

Above, cloned_array uses .clone to create a copy of array_two so that the latter doesn't get mutated. Without the .clone statement, cloned_array would still point to the same value as array_two, so a mutation to one would mutate the other. But with this trick, our original arrays are preserved so that they can be called elsewhere in their original form.

Alternatively, you can also use .dup instead, which operates similarly but has [some differences][clone_vs_dup].



[clone_vs_dup]: https://coderwall.com/p/1zflyg