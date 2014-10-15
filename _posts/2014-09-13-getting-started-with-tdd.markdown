---
layout: post
title:  "Getting Started with Test Driven Development in Ruby"
date:   2014-09-13 
categories: Ruby
tags: tdd rspec beginning
---

One of the things that I've been working on lately is getting more proficient at test driven development (TDD). Though I've known for awhile that TDD is considered a best practice, in the past I've sometimes succumbed to the temptation to try and write a program that does what I want without pausing first to write tests. No mas.

I've decided to invest time in learning TDD for two main reasons: (1) it seems like a pretty fundamental skill. Even if I ultimately move on to some other way of writing maintainable code, I don't think there's any chance I'll regret putting in the time to get some familiarity with TDD at this early point in my learning curve. (2) Tests instill confidence when I start building onto or refactoring my larger programs. Now that I'm getting to a point where I'm producing programs that have more than a few lines of code and multiple features, it's getting a lot scarrier when I go back and mess with things I already did - what if something breaks? Having tests helps me be rest assured that I'll be able to isolate and correct errors before they become big problems.

But even after I became convinced I should start learning TDD, there were still a couple prerequisites I needed to understand to get started. So, in this post, I wanted to lay those out for any other Ruby beginners that are considering testing the TDD waters.

**Number 1: The Tools**

There are a number of tools out there you can use to write tests for Ruby programs. Ruby comes with a testing framework built into its standard library: [Minitest][minitest] (which, as of Ruby 1.9.3 supercedes [Test::Unit][test_unit]), and I've heard good things about [Cucumber][cucumber] and [Capybara][capybara]. But the tool that I've been focusing on the most is [Rspec][rspec].

Rspec is great because its code is very readable, running tests is easy, and there's [a lot of information][rspec_book] out there to help you get started. My lack of familiarity with other testing frameworks perhaps leaves me open to being accused of manifesting some version of [the blub paradox][blub], but I'm comfortable saying Rspec is a great place to start for newbies hoping to get started with testing in Ruby.

Two things you should know, however. First, Rspec uses its own additional syntax (above and beyond what you've encountered if you've only written programs in Ruby). The first time I looked at a program built with Rspec, I saw all of these "describe" and "context" statements that made no sense to me, and I thought my entire Ruby education to that point had been a failure. It hadn't; I was just looking at code written with Rspec. Second, Rspec recently [changed some its syntax][rspec_should_expect]. If you're looking at older files written in Rspec, you're likely to see a lot of code that looks like this:

{% highlight ruby %}
describe "some method" do
  it "should do what I say" do
    some_method(x).should == "what I say"
  end
end
{% endhighlight %} 

But in newer files it'll look like this:

{% highlight ruby %}
describe "some method" do
  it "should do what I say" do
    expect(some_method(x)).to eq("what I say")
  end
end
{% endhighlight %}

If you write code with the old syntax, you'll get a deprecation warning. That annoyed me, so I've been sticking with the newer stuff. But you definitely want to be able to recognize and understand both.

**Number 2: The Process (Red - Green - Refactor)**

TDD isn't just about writing tests to double check that your code does what you want and doesn't break. It's also a process for building efficient and maintainable code from the outset (test *driven* development). Reaping the benefits of that process means following its proper sequence: red, green, refactor.

Red means writing tests that fail. It's important to write the test before you write the code that passes the test - and to make sure the test fails - because if you don't, you don't know that your tests could even possibly fail (a possibility you definitely want to retain if your tests are to be of any use later).

Green means writing the minimal amount of code to make the test pass. A passing test confirms that your code does what you want. It is worth emphasizing, though, that you want to write the *minimal* amount of code to make your tests pass. Getting a test to pass by writing code that carries out more than the objectives specified in your tests installs untested features. Before writing code that does more, write tests that test more.

Refactor means shaping things up so that your code is more elegant and maintainable. You can do the revisions with confidence since passing tests will confirm that everything is still working.

**Final Thoughts**

After picking up some of the tools and getting familiar with the red-green-refactor process, there's still definitely a lot of learning and practice to do. But if you get these two things down, then the rest flows a lot more naturally. 

If you're looking to get some practice in, I would recommend some katas: the prime factors ([instructions][pfinstructions] and [video][pfvideo]) and bowling game ([instructions][bginstructions] and [video][bgvideo]) katas are two of my personal favorites, but I'm sure there's lot of other great ones you could track down as well.

[minitest]: http://ruby-doc.org/stdlib-2.1.2/libdoc/minitest/rdoc/MiniTest.html
[test_unit]: http://www.ruby-doc.org/stdlib-2.1.2/libdoc/test/unit/rdoc/Test/Unit.html
[cucumber]: http://cukes.info/
[capybara]: http://jnicklas.github.io/capybara/
[rspec]: http://rspec.info/
[rspec_book]: https://pragprog.com/book/achbd/the-rspec-book
[blub]: http://en.wikipedia.org/wiki/Paul_Graham_(computer_programmer)#Blub
[rspec_should_expect]: https://www.relishapp.com/rspec/rspec-expectations/docs/syntax-configuration
[pfinstructions]: http://butunclebob.com/ArticleS.UncleBob.ThePrimeFactorsKata
[pfvideo]: http://vimeo.com/7762511
[bginstructions]: http://butunclebob.com/ArticleS.UncleBob.TheBowlingGameKata
[bgvideo]: https://www.youtube.com/watch?v=wrr16PdgHPM
