---
layout: post
title:  "Working With Rspec: Testing Input and Output with Mocks"
date:   2014-10-02 
categories: tdd
--- 

Lately, I've been working on getting more familiar with TDD and Rspec by writing tests first for every method of my programs. Though I've heard some [loud criticisms][tdd_is_dead] of adhering strictly to the test-first design pattern (as well as a [rebuttal][test_first] or [two][response_to_dhh]), I'm mostly interested in giving it a serious try right now so that I can learn all of the techniques and make a more informed judgment about when (not) to test down the line.

For the most part, I've been applying the standard pattern with which I'm most familiar: `expect(some_method).to eq(some_value)`. Making sure my methods behave as I expect, and working through those expectations sequentially, has generally resulted in more elegant code with fewer unnecessary dependencies.

However, one of the things that I've found more tricky is testing input and output. 

At first, I was tripped up by the simple fact that methods which send output to the console always return `nil`. How am I supposed to test different instances of output to the console if all of them will `eq(nil)`?

Next, I discovered [the `output` matcher][output]. If I have a method like this:

{% highlight ruby %}
def say_hello
  print "Hello!"
end
{% endhighlight %}

Then I can test it like this:

{% highlight ruby %}
describe "#say_hello" do
  it "says hello" do
    expect{ say_hello }.to output("Hello!").to_stdout
  end
end
{% endhighlight %}

This felt like a glorious solution. Writing tests for all of my output would be a breeze!

Unfortunately, this approach has some downsides too. 

First of all, it's a real pain for long strings. I end up with massive tests that are just double checking I've correctly copied the exact same text between the spec and lib files - getting very bogged down in the implementation details while verifying very little of my program's behavior. 

Second, this test is going to fail if I decide to make small, relatively inconsequential changes. Say I've written in a typo and want to fix it - now I have to modify my code and my tests. That doesn't seem like an optimal way of doing things.

Facing this dilemma, a mentor advised that I separate out what I'm printing to the console from the operations that do the printing. Now I'm using a mock to test that input and output methods are properly being called, and then passing specific strings to those operations. This has made testing both easier and more useful. Here's how it works:

I begin with an input/output class. This is the place where specific messages will get sent in order to display in the console. It looks like this:

{% highlight ruby %}
class InputOutput
  attr_reader :reader, :writer

  def initialize(reader, writer)
    @reader = reader
    @writer = writer
  end

  def print_to_stdout(message)
    writer.print message
  end

  def get_input
    reader.gets
  end
end
{% endhighlight %}

Now I can run this class in my program by passing `$stdin` and `$stdout` as my arguments for reader and writer, and I can also run tests that don't depend on those inputs by passing in other arguments.

At first, I thought I'd test this class by passing in StringIO objects. I had seen that approach [mentioned][stringio] in some [other posts][stdin] while I was reading on the topic, and it seemed like a good place to start. It helped me test that input and output were conforming to what I'd expect given whatever I passed in.

However, I was still unhappy with some elements of this approach. This may just be germane to errors I was making along the way, but I found that the operations I was performing to make tests pass were resulting in code that wouldn't actually work as I expected, and I didn't like that I had to set whatever input I was going to expect when I initialized new instances of the class.

So, I set out to make a mock. This would be an object that I could pass in for my reader and writer not to see that input and output were conforming to expectations, but rather just to see that the appropriate methods were in fact being called. When I initialize my MockIO, <s>I set certain instance variables to false (e.g. `@print_was_called` or `@gets_was_called`), and</s> I overwrite the appropriate methods in my mock to <s>toggle those values to true</s> *[edit, see below] return unique identifiers [end edit]* if they are indeed called. When my tests passed, I know that I'm in fact calling the methods I wanted to access, and I can pass in whatever values I like.

This approach has the additional benefit of letting me separate out all of the actual text I want to output to the console. I put that stuff a separate class where I have methods that *return* (rather than `puts` or `print`) strings. I can test those methods without using the `output` matcher, simply expecting returns of those method calls to `include("part of the string")`. 

So, altogether, I have a working test suite where the tests are less brittle and the code operates as expected. 

Note: I can tell that there's a lot left to learn about using mocks in different situations. Some of the resources I've found helpful and provactive can be found [here][fowler], [here][little_mocker], [here][dont_get_mocks], and [here][mocks_wrong].

*[Check out my [new post][new_post] on rolling my own mocks to see more about the implementation details]*


[tdd_is_dead]: http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html
[test_first]: http://blog.8thlight.com/uncle-bob/2014/05/19/First.html
[response_to_dhh]: http://mikeknep.com/2014/04/23/response-to-dhh.html
[output]: https://www.relishapp.com/rspec/rspec-expectations/docs/built-in-matchers/output-matcher
[stringio]: http://danielirvine.com/blog/2014/05/30/a-better-stringio/
[stdin]: http://technicalpickles.com/posts/a-pattern-for-using-standard-in-and-out-in-your-ruby-code/
[fowler]: http://martinfowler.com/articles/mocksArentStubs.html
[little_mocker]: http://blog.8thlight.com/uncle-bob/2014/05/14/TheLittleMocker.html
[dont_get_mocks]: http://www.confreaks.com/videos/659-rubyconf2011-why-you-don-t-get-mock-objects
[mocks_wrong]: http://ngauthier.com/2010/12/everything-that-is-wrong-with-mocking.html
[new_post]: {% post_url 2014-10-08-revisiting-mocks %}