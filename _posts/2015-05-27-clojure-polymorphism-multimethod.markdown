---
layout: post
title:  "Refactoring in Clojure: Implementing Polymorphism with Multimethods"
date:   2015-05-27
categories: Clojure
tags: polymorphism solidexit

--- 

Recently, I took a stab at completing the [Gilded Rose kata in Clojure][grc]. The kata was new and different for me, because you begin with a section of code already written. The challenge isn't to quickly implement a simple algorith; it's to thoughtfully decompose an overly-complicated wreck.

One of the main challenges to tackle in Gilded Rose is decomposing a massive set of nested conditionals into a more readable and extensible algorithm. The naive first step is to simply extract methods from each branch of the outermost conditions, but you're still left with code that violates the open-closed principle. New additions will require you to add another branch.

This problem is amplified by the ultimate task of the kata: to add a new item to the store's inventory. You just can't feel good about completing that last step unless you've found some way to render the code open for extension but closed for modification.

So the question that presents itself is how to do so. While an object-oriented language would rely on polymorphism (where each item type would carry its own implementation of the method that updates its quality), Clojure's lack of classes and objects would seem to place such an approach out of bounds. 

Luckily, that's not the case. Enter multimethods.

**Implementing Polymorphism With Multimethods**

Multimethods entail a collection of implementations of a particular function where one is selected for application depending on the nature of the argument(s) passed in. They're defined with one definition that explains how to determine which implementation to select, then a variety of implementations for different arguments.

{% highlight clojure %}
(defmulti respond-to-num (fn [num-map] (:num num-map)))

(defmethod respond-to-num 1 [num-map]
  "The number for this map is 1!")

(defmethod respond-to-num 2 [num-map]
  "The number for this map is 2!")

(defmethod respond-to-num :default [num-map]
  "The number for this map is neither 1 nor 2 :(")

user=> (respond-to-num { :num 1 })
;= "The number for this map is 1!"

user=> (respond-to-num { :num 2 })
;= "The number for this map is 2!"

user=> (respond-to-num { :num 3 })
;= "The number for this map is neither 1 nor 2 :("
{% endhighlight %}

You can see that the first definition uses `defmulti` to define a dispatching function that returns a dispatching value. We take a look at the value associated with the key `:num` in the `num-map` passed in[^1].

Next, we define a set of methods with dispatch values. If the dispatching function returns `1`, Clojure knows to choose the method with the dispatch value of `1`. The same logic applies for `2`, and then we create a default implementation for if neither of the above implementation match the dispatching value.

There are several other ways to implement polymorphism in Clojure[^2], but the multimethod is a great way to tackle the particular problem posed by Gilded Rose. Doing so allows us to transform this: 

{% highlight clojure %}
(defn update-quality [items]
  (map
    (fn[item] (cond
      (and (< (:sell-in item) 0) (= "Backstage passes to a TAFKAL80ETC concert" (:name item)))
        (merge item {:quality 0})
      (or (= (:name item) "Aged Brie") (= (:name item) "Backstage passes to a TAFKAL80ETC concert"))
        (if (and (= (:name item) "Backstage passes to a TAFKAL80ETC concert") (>= (:sell-in item) 5) (< (:sell-in item) 10))
          (merge item {:quality (min 50 (inc (inc (:quality item))))})
          (if (and (= (:name item) "Backstage passes to a TAFKAL80ETC concert") (>= (:sell-in item) 0) (< (:sell-in item) 5))
            (merge item {:quality (min 50 (inc (inc (inc (:quality item)))))})
            (if (< (:quality item) 50)
              (merge item {:quality (inc (:quality item))})
              item)))
      (< (:sell-in item) 0)
        (if (= "Backstage passes to a TAFKAL80ETC concert" (:name item))
          (merge item {:quality 0})
          (if (or (= "+5 Dexterity Vest" (:name item)) (= "Elixir of the Mongoose" (:name item)))
            (merge item {:quality (max 0 (dec (dec (:quality item))))})
            item))
      (or (= "+5 Dexterity Vest" (:name item)) (= "Elixir of the Mongoose" (:name item)))
        (merge item {:quality (max 0 (dec (:quality item)))})
      :else item))
  (map (fn [item]
      (if (not= "Sulfuras, Hand of Ragnaros" (:name item))
        (merge item {:sell-in (dec (:sell-in item))})
        item))
  items)))
{% endhighlight %}

Into this:

{% highlight clojure %}
(defn age-item [item]
  (if (not= (item :name) "Sulfuras, Hand of Ragnaros")
    (merge item { :sell-in (dec (item :sell-in)) })
    item))

(defn age-items [coll]
  (map #(age-item %) coll))

(defmulti adjust-quality (fn [item-name] (item-name :name)))

(defmethod adjust-quality "Sulfuras, Hand of Ragnaros" [item]
  item)

(defmethod adjust-quality "Aged Brie" [item]
  (if (< (item :quality) 50)
    (merge item { :quality (inc (item :quality)) })
    item))

(defmethod adjust-quality "Backstage passes to a TAFKAL80ETC concert" [item]
  (cond
    (< (item :sell-in) 0) (merge item { :quality 0 })
    (< (item :sell-in) 5) (merge item { :quality (min 50 (+ 3 (item :quality))) })
    (< (item :sell-in) 10) (merge item { :quality (min 50 (inc (inc (item :quality)))) })
    :else (merge item { :quality (min 50 (inc (item :quality))) })))

(defmethod adjust-quality :default [item]
  (if (<= (item :quality) 0)
    item
    (if (> 0 (item :sell-in))
      (merge item { :quality (dec (dec (item :quality))) })
      (merge item { :quality (dec (item :quality)) } ))))

(defn update-quality [items]
  (map #(adjust-quality %) (age-items items)))
{% endhighlight %}

There are still improvements to be made, for sure. But we've now decomposed a massive beast of nested conditionals into a manageable collection of bite-sized chunks. And, best of all, we're now satisfying the open-closed principle; adding a new item type requires - at most - that we add another implementation of `adjust-quality`. 

**Conclusion**

It's important not to overuse Clojure's sophisticated facilities for implementing polymorphism, since doing so can provide a crutch to the object-oriented programmer that enables you to dodge more idiomatically functional ways of tackling problems. But when a huge method just screams out for decomposition, resources like Clojure's multimethod can make it easier to accomplish your objectives in a way that yields extensible code. 

[^1]: We could actually just put `:num` for our dispatching function - Clojure knows to treat a keyword as a function. I've used the more verbose form here for clarity as to what exactly is going on. The verbose form can be useful when you want to identify the correct implementation based on some more nuanced evaluation of your argument(s).
[^2]: Check out [this][8L_post] and [this][cftbat] for further information on using functions, protocols, and records to implement the best form of polymorphism for your particular problem. 

[grc]: https://github.com/mjansen401/gilded-rose-clojure
[8L_post]: http://blog.8thlight.com/myles-megyesi/2012/04/26/polymorphism-in-clojure.html
[cftbat]: http://www.braveclojure.com/multimethods-records-protocols/