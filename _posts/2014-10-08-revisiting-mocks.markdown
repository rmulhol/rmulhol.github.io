---
layout: post
title:  "Working with Rspec: Revisiting Testing With Mocks"
date:   2014-10-08 
categories: Ruby 
tags: tdd mocks
--- 

So you've got most of your project built, tests are passing, and the last task that remains is to piece together all of the public interfaces into a working program. Woohoo! You've come quite a ways! Since you did all of the legwork to get here, it probably feels like  a quick jump to reach that pinnacle that is a working final product. The impulse to spike it - to forego the principles of test-first design in favor of a rapid push to make everything run - may be strong. But you must stick to the path. Veering off now will only reintroduce the complications you've managed to avoid so far by doing things the right way.

Presuming, then, that you intend to stay on track, how are you to do so? You could migrate over all of the tests for methods from other classes that you're now introducing into your main class, but that feels pretty suspect (not very DRY, and probably too knowledgeable about what's going on in the other 'black boxes'). You could also reserve tests for checking operations that are being introduced for the first time in the main class (e.g. the way that operations are interacting with one another), but that will leave a broad swath of your code untested. There must be a better way.

One approach that I've been working on recently (and [writing about][using_mocks]) is using mocks. Mocks make it easy to check that your main interface is interacting appropriately with the other classes in your program, without having to know too much about what's going on inside of them. Using mocks, you can check that outgoing command messages get sent without asking too much about the side effects we encounter when they are.

Before proceeding, I should mention two things:

(1) I've opted to 'roll my own' mocks. I know that Rspec and other testing frameworks provide various automated features for constructing test doubles easily and efficiently, but I've [heard][roll_your_own] that avoiding those can yield optimal results. Plus, I'm interested in getting a feel for what's going on beneath the hood before I hop in and start driving the car.

(2) I'm happily indulging in mocks for now, perhaps too much. Though I've encountered some who [claim][wrong_with_mocking] that mocks can make tests brittle, and others who [advise][mock_overuse] simply to avoid overusing mocks, I'm using them a lot to aid my learning; using mocks liberally now will make it easier to use them appropriately when I need them down the line.

So, with that said, how can one write the mocks that let you test that outgoing command messages are sent, and just that?

At first, I was overwriting methods from my (non-mock) classes so that they would return a boolean. Set an instance variable to `false` when the new mock object is initialized, then set it to `true` when the appropriate method is called. For example, my MockIO object initially looked like this:

{% highlight ruby %}
class MockIO
  attr_reader :print_was_called

  def initialize
    @print_was_called = false
  end

  def print(message)
    @print_was_called = true
  end
end
{% endhighlight %}

Now I can inject my MockIO object in place of my real IO object when I'm testing my main class, and then assure I'm receiving the appropriate boolean after the outgoing command messages are sent. For example, if my main class looked like this:

{% highlight ruby %}
class Main
  def intialize(io)
    @io = io
  end

  def print(message)
    io.print(message)
  end
end
{% endhighlight %}

Then I can test it this test:

{% highlight ruby %}
require 'mockio'
require 'main'

describe Main do
  let(:new_run) { described_class.new(MockIO.new) }

  describe "#print_message" do
    it "calls print from io" do
      message = ''
      printed_message = new_run.print(message)

      expect(printed_message).to eq(true)
    end
  end
end
{% endhighlight %}

And, there we have it. I've now rolled my own mock and I can inject it into my tests to assure that outgoing command messages are being sent without having to do anything more. I don't have to incur any side effects the commands might have when they're sent to my real IO, and I don't have to worry about unexpected outcomes I could see using an automated mocking framework. Cool!

But, this approach does have some downsides. First of all, I'm going to be intializing a lot of booleans - not a big problem, but something that irks me. Second, and perhaps most importantly, I'm running the risk of verifying that I've sent the wrong message. If all of my method calls in the mock object return `true`, then a verification that one method has been sent will be indistinguishable from the verification that another method has been sent. I won't be able to verify that I actually sent the *right* method.

So, I've reworked my mocks so that each method returns a unique identifier. Now, instead of returning `true`, the `print` method returns `"print called"`. It looks like this: 

{% highlight ruby %}
class MockIO
  def print(message)
    "print called"
  end
end
{% endhighlight %}

For this MockIO, my test looks like this:

{% highlight ruby %}
require 'mockio'
require 'main'

describe Main do
  let(:new_run) { described_class.new(MockIO.new) }

  describe "#print_message" do
    it "calls print from io" do
      message = ''
      printed_message = new_run.print(message)

      expect(printed_message).to eq("print called")
    end
  end
end
{% endhighlight %}

Everything still works, and I have verification not only that an outgoing command message is being sent, but also that the *right* outgoing command message is being sent.

When I multiply this process for each of the other classes and methods I'm injecting into main, I end up with a nicely tested main class that I'm confident is working properly regardless of what's going on in the other classes that get injected into it. I don't have to worry about side effects cluttering up my test suite, and things run more quickly since the various unit tests are isolated from one another.

[using_mocks]: {% post_url 2014-10-02-testing-with-mocks %}
[roll_your_own]: http://blog.8thlight.com/josh-cheek/2011/11/28/three-reasons-to-roll-your-own-mocks.html
[wrong_with_mocking]: http://ngauthier.com/2010/12/everything-that-is-wrong-with-mocking.html
[mock_overuse]: http://googletesting.blogspot.com/2013/05/testing-on-toilet-dont-overuse-mocks.html