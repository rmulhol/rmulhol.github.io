---
layout: post
title:  "Sorting Data Of a Non-Predetermined Type: Java's Comparable Interface"
date:   2015-02-10
categories: Java
tags: algorithms
--- 

In attempting to learn more about data structures and algorithms, I've been focused on learning the details of the concepts themselves: what distinguishes one data structure from another? What logic makes an algorithm work? That kind of thing.

This has led to some minor achievements. I've been able to [implement insertion sort][prev_post1] in Java. I've learned why it's good to [use an array (rather than a linked list) when writing sorting algorithms][prev_post2]. I know more now that I did when I embarked upon this journey.

But one thing that's been bugging me is the inflexibility of the solutions I've come up with. It's great to be able to sort an array of integers, but what if I want to sort an array of ... any other type of object? Do I have to overload the algorithm with every type of object I intend to sort? 

Nope. Enter Java's [`Comparable` interface][comp].

If a class implements `Comparable`, then the `compareTo()` method provides a [total order][total_order] for all objects of that class. When we call `x.compareTo(y)`, it returns a positive integer (1) if `x` is greater than `y`, 0 if `x` is equal to `y`, or a negative integer (-1) if `x` is less than `y`.

This is a pretty big deal. My previous version of insertion sort looked like this: 

{% highlight java %}
public int[] mySort(int[] list) {
    for(int j = 1; j < list.length; j++) {
        int key = list[j];
        int i = j - 1;
        while(i >= 0 && list[i] > key) {
            list[i+1] = list[i];
            i = i - 1;
        }
        list[i+1] = key;
    }
    return list;
}
{% endhighlight %}

That's right. It only sorts arrays of integers. Lame.

The `Comparable` interface allows us to write one sorting algorithm that works for objects of any class that implements it, like so: 

{% highlight java %}
public Comparable[] mySort(Comparable[] list) {
    for (int j = 1; j < list.length; j++) {
        Comparable key = list[j];
        int i = j - 1;
        while (i >= 0 && list[i].compareTo(key) > 0) {
            list[i + 1] = list[i];
            i = i - 1;
        }
        list[i + 1] = key;
    }
    return list;
}
{% endhighlight %}

Now we have an algorithm that already works for a bunch of built in Java types we might expect to sort (Integer, String, Date, File, etc.), and we can make it work for any classes we create by simply having them implement the `Comparable` interface.

[prev_post1]: {% post_url 2015-01-14-java-insertion-sort %}
[prev_post2]: {% post_url 2015-01-26-linked-list-sorting %}
[comp]: http://docs.oracle.com/javase/8/docs/api/java/lang/Comparable.html
[total_order]: http://en.wikipedia.org/wiki/Total_order