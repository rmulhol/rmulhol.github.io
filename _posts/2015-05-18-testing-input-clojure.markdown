---
layout: post
title:  "Testing Input Validation in Clojure: The Beauty of With-In-Str"
date:   2015-05-18
categories: Clojure
tags: testing validation
--- 

Over the past week, I put together [tic tac toe in Clojure][ttt_clojure]. The game is playable from the command line, the AI player is unbeatable, and it takes up significantly fewer lines of code than any other version of the game I've completed previously.

I was feeling pretty proud of myself, until I saw what happens when the user enters invalid input upon being promped for a move: the dreaded `NullPointerException`. Though my function was set up to recur in the instance of invalid input, something was getting in the way of propogating subsequently validated input back up the stack.

Though there were problems with the mechanics of my code, the bigger problem was the absence of tests. I had tests that simulated valid user input, but none for invalid input. Because invalid input provoked a recursive call, simulating it felt like a real headache for my test suite.

But it turns out that simulating a sequence of input is no headache at all. Just a simple tweak on the exact function used to simulate valid input: `with-in-str`.

Let's take a look at how one might simulate user input with `speclj`:

{% highlight clojure %}
(describe "prompt"
  (it "returns the input provided by the user"
    (should= "hello"
      (with-in-str "hello"
        (prompt "Enter text: ")))))
{% endhighlight %}

Assuming our `prompt` function looks like this:

{% highlight clojure %}
(defn prompt [message]
  (println message)
  (read-line))
{% endhighlight %}

The test passes. The function `with-in-str` provides a string for `read-line` to read, allowing the test to run without actual user input.

Now imagine we want to validate that the user has entered something specific. For example, the string "hello". We could do that like so:

{% highlight clojure %}
(defn prompt [message]
  (println message)
  (let [input (read-line)]
    (if (= input "hello")
      input
      (recur message))))
{% endhighlight %}

But how do we test it? If we do this:

{% highlight clojure %}
(describe "prompt"
  (it "returns the input provided by the user"
    (should= "hello"
      (with-in-str "car"
        (prompt "Enter text: ")))))
{% endhighlight %}

Our tests are going to fail. Not only are they going to fail, they're going to explode. Endless prompts for new user input never quenched by the desired string "hello"; an infinite loop of pain for our test suite.

Fortunately, the function we're using to pass in valid input - `with-in-str` - can take multiple lines. Since our `prompt` function calls `read-line` to evaluate input, it will pass through one line at a time until it returns.

The simple switch:

{% highlight clojure %}
(describe "prompt"
  (it "returns the input provided by the user"
    (should= "hello"
      (with-in-str "car\nhello"
        (prompt "Enter text: ")))))
{% endhighlight %}

Causes our test to pass again. The string before the newline ("car") is read by the first call to `read-line`, and then the rest of the string until another newline ("hello") is read on subsequent calls. Once input satisfies validation, the function returns.

This is one of the more simple ways of simulating input that I've seen. By simply inserting some newlines, `with-in-str` allows you to check how your program interacts with a sequence of (both valid and invalid) input.

[ttt_clojure]: https://github.com/rmulhol/clojure-tic-tac-toe