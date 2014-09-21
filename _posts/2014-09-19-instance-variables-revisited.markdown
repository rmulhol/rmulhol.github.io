---
layout: post
title:  "Initializing Instance Variables: Revisiting Self and Attributes"
date:   2014-09-19 
categories: Class
---

In a [previous post][initial_instance_variable_post], I described the process of initializing instance variables with the self.instance_variable naming format. To do so, you have to use an attr_accessor:

{% highlight ruby %}
class Books
  attr_accessor :title, :author

  def initialize(title, author)
    self.title = title
    self.author = author
  end
end
{% endhighlight %}

This approach has its advantages. You don't, for example, have to worry about a mistyped reference to your instance variable running as nill:

{% highlight ruby %}
class Cats
  attr_accessor :name

  def initialize(name)
    self.name
  end

  def cats_name
    self.naaaame
  end
end

> my_cat = Cats.new("Buford")
> my_cat.name
=> "Buford"
> my_cat.cats_name
=> NoMethodError: undefined method `naaame'
{% endhighlight %}

This NoMethodError doesn't show up if you mistype an instance variable labeled with '@':

{% highlight ruby %}
class Dogs
  attr_reader :name

  def initialize(name)
    @name = name
  end

  def dogs_name
    @naaame
  end
end

> my_dog = Dogs.new("Pug Life")
> my_dog.name
=> "Pug Life"
> my_dog.dogs_name
=> nil
{% endhighlight %}

Though the nil return might not seem like that big of a deal right now, it can be a real pain later if the value gets passed through multiple lines of code before you receive the inevitable 'undefined method for nil: NilClass' error. You'll be wishing you could have seen the error right where you went wrong.

However, there are downsides to the self.instance_variable naming format as well. Being required to use an attr_accessor to get your instance variables up and running risks code leaks and unwanted dependencies, since other objects can now write to that method.

So, what's the solution?

From what I can tell, perhaps the best approach is to hold on to the @instance_variable naming in your initialize method, but then embrace the power of the attr_reader: 

{% highlight ruby %}
class Books
  attr_reader :title, :author

  def initialize(title, author)
    @title = title
    @author = author
  end

  def books_title
    tiiiitle
  end
end

> my_book = Books.new("Nineteen Eighty-Four", "George Orwell")
> my_book.title
=> "Nineteen Eighty-Four"
> my_book.books_title
> undefined local variable or method 'tiiiitle'
{% endhighlight %}

Typos will only run as nil when you include the '@' at the beginning of the instance variable. Taking advantage of the attr_reader and referring to your instance variables without that symbol takes care of that, and doesn't require you to include an attr_writer.

Ultimately, choosing how to initialize and refer to your instance variables is a decision you make based on your project's specific needs - you can go either way. But, in general, holding onto the '@' and taking advantage of the attr_reader seems to deliver most of the benefits and fewer of the costs associated with alternative approaches. 

[initial_instance_variable_post]: {% post_url 2014-09-16-initializing-instance-variables %}