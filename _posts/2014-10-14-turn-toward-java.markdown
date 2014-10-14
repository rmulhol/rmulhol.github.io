---
layout: post
title:  "A Turn Toward Java: First Impressions"
date:   2014-10-14
categories: Java
--- 

Recently, I started writing code in Java. I certainly haven't finished learning Ruby, but the impulse to stretch out toward other languages is something I could no longer suppress. I'm hoping that working with a 'closer to the metal' object-oriented language will give me some fresh perspective on what I can accomplish, and I'm also looking to get familiar with the process of learning new languages; I suspect that's a skill I'll put to use many times over the next few years.

One of the first things that struck me about Java is its verbosity. In Ruby, the 'hello world' program looks like this:

{% highlight ruby %}
puts 'Hello, world!'
{% endhighlight %}

But in Java, it looks like this:

{% highlight java %}
class HelloWorld {
  public static void main(String args[]) {
    System.out.println("Hello, world!");
  }
}
{% endhighlight %}

While Ruby does a lot of work behind the scenes and lets you accomplish tasks with really elegant and readable code, Java requires a little more legwork. You can't just execute one command to print "Hello, world!" to the console; you also have to create a class, declare a `main` method, use proper opening and closing curly braces, *and* be sure to end every statement with a semicolon.

Another thing that distinguishes Java from Ruby is that it's a 'strong static' language (Ruby is 'strong dynamic'). What this means is that you must declare types for variables and method returns, and then compile source code into 'bytecode' before it can run. While Ruby lets you write code and then immediately run it with the `ruby <filename>` command, Java requires you to first enter `javac <filename>` (to compile) and then enter `java <classname>` (to run). If your source code contains obvious errors, it won't compile and (therefore) it won't run.

While the above distinctions might make Java sound like more trouble than it's worth, there are some merits to these features. 

First, all of the verbosity paints a better picture of what's going on 'under the hood'. While I've encountered some baffling errors in Ruby where the code didn't do what I expected, it's hard to even get to those sort of lofty expectations in Java since you're laying out each step in greater detail.

Second, the process of compiling is really great for both learning the language and catching errors. While some errors are sure to get through either way, the failed compilations help you to recognize many of them before they impact your code. They also train you to start writing code the right way from the outset - you can only encounter so many compilation errors from failing to include a type or a semicolon before including those things becomes second nature.

Above and beyond the merits of its features, knowing a little bit about Java has certainly yielded some positive dividends for learning about programming more generally. So many of the recommended books I've been working through have examples written in Java, and those used to look like Greek to me. Now they're starting to make more sense, and I'm feeling like previously indecipherable resources are becoming more accessible.

So, unward and upward. To Java, and beyond!

Oh, by the way, no chance I'm changing the name of my blog. We've got a history.