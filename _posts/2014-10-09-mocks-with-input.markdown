---
layout: post
title:  "More on Mocks: Passing in Input"
date:   2014-10-09 
categories: tdd
--- 

In a couple of [previous][using_mocks] [posts][revisiting_mocks], I've been documenting my foray into the world of mocks. So far, I've been using mocks to verify that outgoing messages get sent without incurring unwanted side effects. To do so, I've overridden the methods from my real objects so that their mock versions return a unique identifier. For example, my mockIO looked like this:

{% highlight ruby %}
class MockIO
  def print(message)
    "print was called"
  end

  def gets
    "gets was called"
  end
end
{% endhighlight %}

I find this formatting helpful because it does what I want and not more - I test that `print` and `gets` are called, but I don't get things printing to the screen and/or prompting for input (as I would with the real versions). As a bonus, the unique identifiers assure me that I'm calling the *right* methods; I don't have to worry about the possibility that my tests are just confirming that *a* method was called (i.e. if I set all the methods in my mock to return a boolean, I could get a `true` returned even if I called the wrong method).

Recently, however, I've run into a problem: some of the logic of my program relies on taking and using user input. In order to test that, I want to make sure that those methods continue prompting for user input until the get an appropriate response. For example:

{% highlight ruby %}
def get_input
  response = ''
  until response.to_i >= 0 && response.to_i <= 4
    response = io.gets
  end
end
{% endhighlight %}

Putting aside the numerous refactorings that could make this code better (I've put in several responsibilities to clarify the context), the irreducible nature of my problem is that my standard mock is going to break the test. `get_input` keeps waiting and waiting for an integer between 0 and 4, but my mock `gets` only ever returns "gets was called".

So, of course, I could separate out the code following the `until` statement and test that, but I'm still going to want some way of verifying that the loop keeps going until it receives valid input (and I'm also going to want that test to eventually *provide* valid input, so that the test doesn't keep waiting indefinitely). 

One way I've come up with to do that is to pass in input to my mock. Now the mockIO can look like this:

{% highlight ruby %}
class MockIO
  def initialize(input)
    @input = input ||= ["gets was called"]
  end

  def print(message)
    "print was called"
  end

  def gets
    @input.shift
  end
end
{% endhighlight %}

Now, when I initialize an instance of my main class in the tests, I can inject in an instance of my mockIO with whatever input I want to execute. For example, if I pass in `['-7', '13', '2']` to my mockIO and run tests on `get_input`, I can expect that method to return 2. 

[using_mocks]: {% post_url 2014-10-02-testing-with-mocks %}
[revisiting_mocks]: {% post_url 2014-10-08-revisiting-mocks %}