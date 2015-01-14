---
layout: post
title:  "Learning Data Structures and Algorithms: Implementing Insertion Sort in Java"
date:   2015-01-14
categories: Java
tags: algorithms
--- 

One of the things that I've been working on lately is getting more familiar with data structures and algorithms. I've had a lot of fun working with different programming languages, styles, and frameworks, but I've been itching to develop some of the more foundational knowledge that I would have gotten had I pursued a CS degree in college.

As a jumping off point, I picked up [Introduction to Algorithms][intro_algorithms] by Cormen, Leiserson, Rivest, and Stein (CLRS). I'm sure that there are lots of other good places that one could start with to learn about these materials, but this book came [highly recommended][how_to_learn] and I figured it was more important to get started than to find a perfect starting point.

I'm only a couple chapters in, and I can already see why this book would be considered required reading for someone pursuing a career in software development. The materials are simple enough for the average reader to grasp upon a close read, but challenging enough to keep your interest and stretch your brain.

One of the first algorithms covered in the book is [insertion sort][insertion_sort]. A sorting algorithm, insertion sort sequentially examines each element in a list and moves the element it's looking at into the proper placement among all those that came before. They give the example of a hand of cards: if I'm sorting five cards, I might work through them left to right and move lower cards to the left until the lowest is all the way to the left and the highest is all the way to the right. 

Grokking the algorithm is simple enough; their example makes things really clear. But actually implementing it can be a different matter. Though it's certainly not (even close to) the most challenging algorithm, you have to know a bit about lists, loops, and iterators, and you have to be able to translate their pseudocode. That's the subject of this post.

Their pseudocode for the insertion sort algorithm is as follows:

{% highlight C %}
for j = 2 to A.length
  key = A[j]
  i = j - 1
  while i > 0 and A[i] > key
    A[i + 1] = A[i]
    i = i - 1
  A[i + 1] = key
{% endhighlight %}

Alright, simple enough. As the algorithm describes, we're going through the list, starting with second element, moving it to first if the second is less than the first, and then continuing to shift later elements closer to the beginning until we have a sorted list. Now what if I wanted to implement this pseudocode in Java?

The first thing to note is that there are no type declarations. We'll presumably be using this algorithm to sort a list of integers, so we'll have to note that as we declare variables while translating the pseudocode. Sorting elements of some other type would require some significant changes.

Second, they're indexing the second element of the list at index 2 and the first element at index 1. But the indexes for a LinkedList (which I'll be using) in Java start at 0. This has two consequences: (1) we'll want to initialize our `for` loop at 1 rather than 2, and (2) we'll want to proceed as long as the index is *less than* the length of the list (rather than less than *or equal to* the length, which would take us out of bounds).

Put things together, and an implementation of insertion sort in Java could look like this:

{% highlight java %}
public LinkedList<Integer> insertionSort(LinkedList<Integer> list) {
  for(int j = 1; j < list.size(); j++) {
    int key = list.get(j);
    int i = j - 1;
    while(i >= 0 && list.get(i) > key) {
      list.set(i+1, list.get(i));
      i = i - 1;
    }
    list.set(i+1, key);
  }
  return list;
}
{% endhighlight %}

So, much more to learn and many more algorithms with which to get familiar, but that work can now proceed with a proper understanding and way of translating the pseudocode in place.

[intro_algorithms]: http://www.amazon.com/Introduction-Algorithms-Edition-Thomas-Cormen/dp/0262033844
[how_to_learn]: http://www.forbes.com/sites/quora/2013/07/03/how-do-i-strengthen-my-knowledge-of-data-structures-and-algorithms/
[insertion_sort]: http://en.wikipedia.org/wiki/Insertion_sort