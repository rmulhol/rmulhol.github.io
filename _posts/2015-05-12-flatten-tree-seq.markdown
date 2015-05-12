---
layout: post
title:  "Hand-Rolling Flatten: Coming to Grips with Tree-Seq"
date:   2015-05-12
categories: Clojure
tags: lazy sequences 
--- 

As I've been learning Clojure, one great place I've found to practice is [4Clojure.com][4clojure]. Their challenges run the gamut from the very simple to the quite difficult, and I've found just about all of them valuable. 

Recently, I was working through a challenge that asks you to hand-roll the function `flatten`, which unstacks a nested collection:

{% highlight clojure %}
user=> (flatten [1 2 3])
;= (1 2 3)

user=> (flatten [1 [2 3]])
;= (1 2 3)

user=> (flatten [1 [2 [3]]])
;= (1 2 3)
{% endhighlight %}

In my case, I initially came up with an inelegant solution:

{% highlight clojure %}
(defn flatify [x]
  (loop [input-list x
         output-list []
         reserve []]
    (cond
      (and (empty? input-list) (empty? reserve)) output-list
      (and (empty? input-list) (not (empty? reserve))) (recur reserve output-list [])
      (not (coll? (first input-list))) (recur (rest input-list) (concat output-list [(first input-list)]) reserve)
      (coll? (first input-list)) (recur (first input-list) output-list (rest input-list)))))
{% endhighlight %}

It ain't pretty, but it works.

Still, I figured that their had to be a better way. And, indeed, there is - [check out the Clojure source][cloj_source]:

{% highlight clojure %}
(defn flatten [x]
  (filter (complement sequential?)
          (rest (tree-seq sequential? seq x))))
{% endhighlight %}

Wow, that's elegant. But how does it work? Let's dive in to see what's going on here.

The function definition is the same (`(defn flatten [x]`) as my earlier implementation, but the body is very different. 

First comes a call to `filter`, which takes a predicate and a collection and then returns a new collection containing whichever elements satisfy the predicate. A simple example:

{% highlight clojure %}
user=> (filter odd? [1 2 3 4 5 6])
;= (1 3 5)
{% endhighlight %}

Next, the `flatten` definition includes a predicate for `filter`: `(complement sequential?)`. `complement` returns a new function inverting the function that's passed in, and `sequential?` returns true if the collection passed in implements `Sequential`, so we're saying to filter out all elements that implement `Sequential`.

At this point in digesting the code, I was a little confused. We want to *flatten* the collection, not merely weed out any nested elements. And, indeed, without the final line, that's precisely what it'd do:

{% highlight clojure %}
user=> (defn eager-flatten [x] 
          (filter (complement sequential?) x))
;= #'user/eager-flatten

user=> (eager-flatten [1 [2 3] 4])
;= (1 4)
{% endhighlight %}

The key to `flatten`, then, is the final line. And, more specifically, the `tree-seq` operation.

Checking out [the docs][tree_seq_docs], we can see that `tree-seq` performs a depth-first walk, returning a lazy sequence of the nodes in a tree. It takes three arguments: a function which returns true if passed a node that can have children (`branch?`), a function that returns a sequence of the children of a node (`children`), and a root node (`root`).

Let's see it in action:

{% highlight clojure %}
user=> (tree-seq sequential? seq [1 2 3])
;= ([1 2 3] 1 2 3)

user=> (tree-seq sequential? seq [1 [2 3]])
;= ([1 [2 3]] 1 [2 3] 2 3)

user=> (tree-seq sequential? seq [1 [2 [3]]])
;= ([1 [2 [3]]] 1 [2 [3]] 2 [3] 3)
{% endhighlight %}

Taking a look at the output, you can see that we're getting a copy of the original collection (the root node) followed by a depth-first traversal of that collection as a tree. That is, we visit each element, checking whether it satisfies `sequential?`. If so, then we treat it as a branch node and evaluate its children recursively until we reach a terminal node. If not, then we know we have reached a terminal node and can move on to the next element.

Conveniently, the collection of terminal nodes is precisely what we're looking for with `flatten`. Everything else falls into place. Returning to the source:

{% highlight clojure %}
(defn flatten [x]
  (filter (complement sequential?)
          (rest (tree-seq sequential? seq x))))
{% endhighlight %}

We call `rest` on the output to discard the root node[^1]. We filter out every non-terminal node (i.e. each node that satisfies `sequential?`), and we're left with a single collection of terminal nodes - an unstacked version of the original collection.

[^1]: We could actually remove `rest` from the function definition and everything would still work fine, but there's no point in evaluating the first element since we know it's going to be the original collection.

[4clojure]: http://www.4clojure.com/
[flatten_docs]: https://clojuredocs.org/clojure.core/flatten
[cloj_source]: https://github.com/clojure/clojure/blob/clojure-1.6.0/src/clj/clojure/core.clj#L6580
[tree_seq_docs]: https://clojuredocs.org/clojure.core/tree-seq