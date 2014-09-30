---
layout: post
title:  "Working with Rspec: Testing Both Individual Files and Complete Projects"
date:   2014-09-26 
categories: TDD
---

In a [previous post][readme_post], I mentioned that it's good to indicate how to run your project's tests (among other things) in your Readme. This makes it easier for others to use your file, and it forces you to reflect on whether or not everything is operating the way you originally intended.

When I did that reflecting for one of my own projects, I was bugged by the fact that I couldn't test my individual specs; the only way I could run any tests was to run the whole test suite. So, I did some poking around and, ladies and gentleman, I think I've found the solution.

But, before I get there, I want to take this opportunity to describe the process that I went through to get here. I'll share some of the hiccups I had along the way, and then reveal the (devastatingly) simple solution.

**Step 1: Testing an Individual File**

When I first began using Rspec, I started trying to write a test for a simple method. I would write a spec like this:

{% highlight ruby linenos %}
require 'addition'

describe "#addition" do
  it "adds 2 integers" do
    expect(addition(1, 1)).to eq(2)
  end
end
{% endhighlight %}

Then I would navigate to my project's spec directory, and type `rspec addition_spec.rb`. The result looked like this: 

{% highlight ruby %}
=> `require': cannot load such file -- addition (LoadError)
{% endhighlight %}

OK, red-green-refactor, right? That's red-ish.

But I simply could not get this error to go away. Creating an addition.rb file in my lib directory did nothing; I couldn't get the tests to run. 

So it was with supreme satisfaction that I learned a way around my problem: add the file directory path! I changed line 1 from the spec file above to this: 

{% highlight ruby %}
require '../lib/addition.rb'
{% endhighlight %}

And everything worked!

My 'require' statement was a little verbose, but the tests were running. I was happy. 

I ran forward with my new knowledge and learned how to [write tests for instance methods][methods_to_classes_post]. 

**Step 2: Testing a Whole Project**

As time wore on, I began to grow weary of my lengthy 'require' statements, and I developed a keen interest in learning better ways of writing tests.

Acting upon that interest, I decided to strike up a conversation with a more knowledgeable compatriot, and I was rewarded with a helpful bit of knowledge. It turned out that if I navigated to my project's home directory (rather than the spec directory) and simply typed `rspec`, all of my tests would run with the original, less verbose `require` syntax (e.g. `require 'addition'`).

I couldn't get the old approach to work, but no matter. I was running all of my tests and using the `require` syntax I had longed for.

Traveling along in this fashion, I began to tackle projects made up of multiple files.

**Step 3: Testing Individual Files and the Whole Project (The Wrong Way)**

As had been the pattern previously, I once again grew tired of the seemingly unnecessary limitations I encountered. Though testing the whole project covered all of my bases, I wondered why typing `rspec addition_spec.rb` in my spec directory would no longer work (instead raising the familiar `LoadError`).

So, I decided to start checking out some [resources][ruby_for_newbies] for Rspec beginners, intending to retrace my steps until I located the crucial mistake that I most certainly must have made. 

One thing that jumped out at me during this process was a suggested use of the spec_helper.rb file.

Basically, the idea was to `require_relative` all of the files in the lib directory in my spec_helper, and then to require only the spec_helper in every spec. Though I felt some apprehension at the idea that every individual spec would require every file in the lib directory, I was desperate.

And it worked! Now I could test the whole project (`rspec spec` in the project directory) and individual files (`rspec addition_spec.rb` in the spec directory), though the tests on individual files weren't outputting the normal colors I was used to (red output for failures, green output for passing). No matter, I thought.

**Step 4: Testing Individual Files and the Whole Project (The Right Way)**

OK, the lack of colors bothered me. What was up with that? 

I decided to do some more research, and I found [this][test_files].

You have *got* to be kidding me.

Duh! The way to run individual tests is to \*stay\* in the project directory and \*direct\* rspec to your intended spec, like so:

{% highlight ruby %}
rspec spec/addition_spec.rb
{% endhighlight %} 

Now I can test individual files (even individual lines), and I get the colored syntax I'm expecting. And, of course, I can still test the whole project. Bonus: no need to use a spec_helper.

So, that took too long, but I learned how to keep my clean 'require' calls, test individual files, also test whole projects, *and* keep the nice syntax coloration that lets me know whether I'm in the red or green stage of red-green-refactor.

Happy coding, y'all.

[readme_post]: {% post_url 2014-09-23-start-with-readme %}
[methods_to_classes_post]: {% post_url 2014-09-14-testing-instance-methods %}
[ruby_for_newbies]: http://code.tutsplus.com/tutorials/ruby-for-newbies-testing-with-rspec--net-21297
[test_files]: http://qugstart.com/blog/ruby-and-rails/rspec-running-one-test-at-a-time/