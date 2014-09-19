---
layout: post
title:  "Initializing Instance Variables: Self and Attributes"
date:   2014-09-16 
categories: class
---

Recently, I learned that you can initialize an instance variable in two different ways. I already knew that instance variables were commonly initialized with an @ sign (as in, @instance_variable), but what's new to me is that you can also do so with the prefix self. (as in, self.instance_variable). 

For example, say I want to write a new class, one called Books that will store titles for each instance. 

Of course, we start with a test: 

{% highlight ruby %}
require 'books'

describe Books do
  let(:new_book) {described_class.new("Nineteen Eighty-Four")}

  it "begins with a title" do
    expect(new_book.title).to eq("Nineteen Eighty-Four")
  end
end
{% endhighlight %}

In the past, I would have tried to make the test pass by writing something like this:

{% highlight ruby %}
class Books
  attr_reader :title

  def initialize(title)
    @title = title
  end
end
{% endhighlight %}

And that's fine. The tests pass.

But what if I wanted to try out this new naming convention? Well, I would start by replacing the @ with self.:

{% highlight ruby %}
class Books
  attr_reader :title

  def initialize(title)
    self.title = title
  end
end
{% endhighlight %}

But this on its own fails the tests:

{% highlight ruby %}
=> NoMethodError: undefined method for 'title='. 
{% endhighlight %}

So what's going wrong? Is this whole alternative naming convention just a sham? Nope.

First of all - special thanks to [Diana][diana] and my other fellow student apprentices for helping me figure this out. 

It turns out that initializing instance variables with self. requires more than just an attr_reader. It also needs an attr_writer or, more elegantly, just an attr_accessor (which combines attr_reader and attr_writer). 

Using attr_writer lets you set the value of an attribute (in this case, the instance variable 'title'), and attr_reader lets you call the value of that attribute elsewhere.

To help flesh this out, say we wanted an option to add an author to our instances. We don't want to require that new instances pass in an author argument when they're initialized (since having an author is optional), so we create a separate method to set the value of author. Test first:

{% highlight ruby %}
require 'books'

describe Books do
  let(:new_book) {described_class.new("1984")}

  it "begins with a title" do
    expect(new_book.title).to eq("1984")
  end

  it "allows authors to be added" do
    expect(new_book.author=("George Orwell")).to eq("George Orwell")
  end
end
{% endhighlight %}

Put in the method to write our author attribute: 

{% highlight ruby %}
class Books
  attr_accessor :title

  def initialize(title)
    self.title = title
  end

  def author=(author)
    @author = author
  end
end
{% endhighlight %}

The tests pass. Now create tests for a method to read that attribute:

{% highlight ruby %}
require 'books'

describe Books do
  let(:new_book) {described_class.new("1984")}

  it "begins with a title" do
    expect(new_book.title).to eq("1984")
  end

  it "allows authors to be added" do
    expect(new_book.author=("George Orwell")).to eq("George Orwell")
  end

  it "allows authors to be called" do
    new_book.author=("George Orwell")
    expect(new_book.author).to eq("George Orwell")
  end
end
{% endhighlight %}

Make it pass:

{% highlight ruby %}
class Books
  attr_accessor :title

  def initialize(title)
    self.title = title
  end

  def author=(name)
    @author = name
  end

  def author
    @author
  end
end
{% endhighlight %}

Everything works.

But, as I'm sure you might have already guessed, there is a better way! What we're doing here is *writing* and *reading* **attributes** for instances of our Books class. And, wouldn't you know it, that's exactly where attr_accessor is most helpful. We could replace the whole class we just completed like this: 

{% highlight ruby %}
class Books
  attr_accessor :title, :author

  def initialize(title)
    self.title = title
  end
end
{% endhighlight %}

All the tests still pass.

When you use the self. technique of initializing an instance variable with attr_accessor, you're carrying out a very similar process. The key diffence is that attr_writer is using the argument you pass in when you initialize a new instance of the class.

<s>Bonus: self.instance_variable will return an undefined method error for the instance variable if you include a typo, while @instance_variable with a typo will return nil (but run just fine). Maybe this approach can help isolate and rectify errors more quickly by making them more explicit.</s>
<br/>

*[See my [newer post][new_post_on_instance_variables] about how to use the @instance_variable naming format with attr_reader to avoid the risks associated with typos, and also avoid unwanted code leaks]*

[diana]: http://calvached.github.io/
[new_post_on_instance_variables]: {% post_url 2014-09-19-instance-variables-revisited %}