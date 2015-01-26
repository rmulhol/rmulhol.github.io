---
layout: post
title:  "Sort a LinkedList? Revisiting Insertion Sort in Java"
date:   2015-01-26 
categories: Java
tags: algorithms
---

In a [previous post][prev_post], I showed a possible implementation of insertion sort written in Java. The code is an almost verbatim translation of pseudocode from [CLRS][clrs].

*Almost* verbatim. While the pseudocode doesn't include any type declarations, my Java code does. Specifically, I chose to work with a [LinkedList][linked_list], since a data structure that allows for [easy *insertion* and deletion of elements][ll_advs] felt like a natural fit for an algorithm named *insertion* sort. The code ended up looking like this: 

{% highlight java %}
public LinkedList<Integer> mySort(LinkedList<Integer> list) {
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

Now imagine my dismay when I read [this post][yegge] about how to succeed in an interview at Google. Specifically, note his comment about my chosen data structure: "For God's sake, don't try sorting a linked list during the interview".

Don't try sorting a linked list during the interview? I just tried sorting a linked list *on the internet*. My tests said that the algorithm worked, too; what was I doing wrong?

Upon deeper investigation, I found some [virulent criticism of the linked list][crit_ll], and decided it was time to revisit my implementation of insertion sort, taking note of a few things:

-- Retrieving elements from a linked list by index, it turns out, is computationally expensive. Specifically, the `.get()` operation I use to access values [is an O(n) operation][ll_perf]; since a LinkedList is a [sequential access list][seq_list], accessing elements by index [takes linear time][lin_time] (i.e. you have to traverse all prior elements in the list until the desired index is reached). 

By contrast, `.get()` [is an O(1) operation][al_perf] for an [ArrayList][array_list]; elements can be accessed by index [in constant time][cons_time] since this data structure is a [random access list][ran_list].

-- My algorithm doesn't actually require *inserting* and *deleting* elements. Though the name "insertion sort" might suggest otherwise, what's really happening is that values are being *swapped* (replaced, rather than inserted and deleted). [An ArrayList excels with `.get()` and `.set()` methods, while a LinkedList excels with `.add()` and `.remove()`][al_ll_compare] --- and I'm only using the former pair!

So in the interest of performance, I decided it might be better to use an ArrayList. That's a quick fix: just change the method return and parameter types from `LinkedList<Integer>` to `ArrayList<Integer>`.

But, upon reflection, even this data structure felt unnecessary. I don't need to dynamically modify the size of the input, so I can just work with a simple [array][array].

That code now looks like this: 

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

... and it works just fine. 

[prev_post]: {% post_url 2015-01-14-java-insertion-sort %}
[clrs]: http://en.wikipedia.org/wiki/Introduction_to_Algorithms
[linked_list]: http://docs.oracle.com/javase/7/docs/api/java/util/LinkedList.html
[ll_advs]: http://en.wikipedia.org/wiki/Linked_list#Advantages
[yegge]: http://steve-yegge.blogspot.com.es/2008/03/get-that-job-at-google.html
[crit_ll]: https://kjellkod.wordpress.com/2012/02/25/why-you-should-never-ever-ever-use-linked-list-in-your-code-again/
[ll_perf]: http://java-performance.info/linkedlist-performance/
[seq_list]: http://docs.oracle.com/javase/7/docs/api/java/util/AbstractSequentialList.html
[lin_time]: http://www.cs.rpi.edu/~musser/gp/List/lists1.html
[al_perf]: http://beginnersbook.com/2013/12/difference-between-arraylist-and-linkedlist-in-java/
[array_list]: http://docs.oracle.com/javase/7/docs/api/java/util/ArrayList.html
[cons_time]: http://docs.oracle.com/javase/tutorial/collections/implementations/list.html
[ran_list]: http://docs.oracle.com/javase/7/docs/api/java/util/RandomAccess.html
[al_ll_compare]: http://www.programcreek.com/2013/03/arraylist-vs-linkedlist-vs-vector/
[list]: http://docs.oracle.com/javase/7/docs/api/java/util/List.html
[array]: http://docs.oracle.com/javase/7/docs/api/java/util/Arrays.html