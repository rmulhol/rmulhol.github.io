---
layout: post
title:  "Writing Tests for Instance Methods in Ruby"
date:   2014-09-14 
categories: TDD
---

Once I decided to [get started with TDD in Ruby][get_started], I developed a premature sense of pride in my ability to use TDD to write methods. Following the red-green-refactor model, I would write a test for a method, write the code to make it pass, revise for elegance and maintainability, and then repeat those steps until the method accomplished everything I wanted. But as soon as I got ambitious and started trying TDD with classes, I ran into some trouble: a NameError telling me of some “undefined local variable or method.”

What was going wrong? Well, as I progressed from files containing single methods to full classes, I was writing tests for *instance* methods without initializing an instance of that class upon which I would call those methods in my tests.  

So, for example, lets say I wanted to write a method that returns “Hello, world!” Simple enough. First, I’d write a test in my project’s spec directory:

{% highlight ruby %}
require ‘hello_world’

describe “hello_world” do
  it “returns ‘Hello, World!’” do
    expect(hello_world).to eq(“Hello, world!”)
  end
end
{% endhighlight %}

Watch it fail. Generate a blank file in my projects lib directory named hello_world.rb. Watch it fail again. Now add some code:

{% highlight ruby %}
def hello_world
  “Hello, world!”
end
{% endhighlight %}

Run the tests again. Everything passes. Grrreat!

But what if I wanted that method to be nested in a class? Well, I’ve already written the tests, so lets add the class and see what happens. Back to the hello_world.rb file:

{% highlight ruby %}
class HelloWorld
  def hello_world
    “Hello, world!”
  end
end
{% endhighlight %}

Run the test:

{% highlight ruby %}
$ rspec spec
F

Failures:

  1) hello_world returns 'Hello, world!'
     Failure/Error: expect(hello_world).to eq("Hello, world!")
     NameError:
       undefined local variable or method `hello_world' for #<RSpec::ExampleGroups::HelloWorld:0x000001011c6ba8>
     # ./spec/hello_world_spec.rb:5:in `block (2 levels) in <top (required)>'

Finished in 0.00071 seconds (files took 0.15835 seconds to load)
1 example, 1 failure

Failed examples:

rspec ./spec/hello_world_spec.rb:4 # hello_world returns 'Hello, world!'
{% endhighlight %}

How can this be? I haven’t changed the method!

Well, now that I’ve nested my (instance) method inside of a class, the test needs to initialize an instance upon which it can call my method. Changing the tests like so solves our problem:

{% highlight ruby %}
require ‘hello_world’

describe “hello_world” do
  it “returns ‘Hello, World!’” do
    my_instance = HelloWorld.new
    expect(my_instance.hello_world).to eq(“Hello, world!”)
  end
end
{% endhighlight %}

So, altogether, not too complicated. Instance methods require tests that initialize instances of the class they intend to test. 

Bonus side note: if you’re testing multiple instance methods, you can DRY up your code by initializing your instance just once like so:

{% highlight ruby %}
require ‘hello_world’

describe “hello_world” do
  let(:my_instance) {described_class.new}

  it “returns ‘Hello, World!’” do
    expect(my_instance.hello_world).to eq(“Hello, world!”)
  end
end
{% endhighlight %}

Though, I must advise that you proceed with caution, as I’ve heard that relying on “let” [isn’t necessarily a best practice][lets_not].

[get_started]: {% post_url 2014-09-13-getting-started-with-tdd %}
[lets_not]: http://robots.thoughtbot.com/lets-not