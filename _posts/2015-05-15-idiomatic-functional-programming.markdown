---
layout: post
title:  "Idiomatic Functional Programming: The Importance of Immersion"
date:   2015-05-15
categories: Clojure
tags: functional idioms 
--- 

Today, I had the privilege of pairing with a colleague who is also focused on learning Clojure. While I come from a background in object-oriented programming, his is in functional programming. Getting a chance to work together and compare how we would each approach different problems was enlightening.

For instance, one problem that we worked on involved trying to design a function that packs a sequence:

{% highlight clojure %}
user=> (pack '(1))
;= ((1))

user=> (pack '(1 2))
;= ((1) (2))

user=> (pack '(1 1))
;= '((1 1))

user=> (pack '(1 1 2 3 3))
;= ((1 1) (2) (3 3))
{% endhighlight %}

I had already developed a working approach to this problem, but I wasn't too happy with the result:

{% highlight clojure %}
(pack [x]
  (loop [input-list x
         output-list []
         last-element nil
         backlog []]
    (cond
      (and (empty? input-list) (nil? last-element)) output-list
      (and (empty? input-list) (empty? backlog)) (recur [] (concat output-list [[last-element]]) nil [])
      (empty? input-list) (recur [] (concat output-list [backlog]) nil [])
      (nil? last-element) (recur (rest input-list) output-list (first input-list) backlog)
      (and (= last-element (first input-list)) (empty? backlog)) (recur (rest input-list) output-list last-element (concat backlog [last-element] [(first input-list)]))
      (= last-element (first input-list)) (recur (rest input-list) output-list last-element (concat backlog [(first input-list)]))
      (empty? backlog) (recur (rest input-list) (concat output-list [[last-element]]) (first input-list) [])
      :else (recur (rest input-list) (concat output-list [backlog]) (first input-list) []))))
{% endhighlight %}

It's so ugly that it doesn't really merit further comment. Suffice it to say I'm basically replicating a `for` loop where a set of variables are slowly pieced together to generate the data I'd like to return.

Letting my partner do the driving, we came up with a much nicer solution:

{% highlight clojure %}
(defn find-similar [coll result]
    (cond (empty? coll) result
          (= (first coll) (second coll)) (find-similar (rest coll) (inc result))
          :else result))

(defn pack [coll]
  (if (empty? coll)
      []
      (let [similar-elements-count (find-similar coll 1)]
        (cons (take similar-elements-count coll)
          (pack (drop similar-elements-count coll))))))
{% endhighlight %}

The key to this (much more elegant) solution is it's effective use of the `take` and `drop` functions - something we found relevant in approaching numerous other problems:

{% highlight clojure %}
(defn rotate-seq [rotate-by coll]
  (let [rotate-by (mod rotate-by (count coll))]
    (concat (drop rotate-by coll) (take rotate-by coll))))

(defn my-partition [part-by coll]
  (when (<= part-by (count coll)) 
    (cons (take part-by coll) (my-partition part-by (drop part-by coll)))))
{% endhighlight %}

The lesson I've taken away is that learning new things in programming means fully immersing yourself in the idioms of the language/model with which you're working. With a bit of creativity, it's usually possible to replicate familiar ways of solving problems - even if they take away your `for` loops and mutable data. But replicating old ways of doing things stands in the way of learning other ways that are new and better. In this case, I'm super happy to have had the chance to pair with a colleague able to show me some new and better ways of doing things.