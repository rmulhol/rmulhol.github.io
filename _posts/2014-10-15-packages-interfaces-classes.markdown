---
layout: post
title:  "Working with Packages: Compiling Classes that Implement Interfaces"
date:   2014-10-15
categories: Java
tags: packages interfaces classes
--- 

To get more familiar with Java, one of the resources I've been working through is [*Java: The Complete Reference, Ninth Edition*][java_reference]. It's a bit of a long slog (the book is 1312 pages), but I've enjoyed getting a fairly comprehensive overview of the language while recreating the numerous example programs offered throughout.

Though I use an IDE ([IntelliJ][intellij]) for my own projects, I've been completing the exercises from the book in Vim for two reasons: (1) I want to get a good understanding of what's going on 'behind the curtain,' without relying on all the legwork IntelliJ does for me; and, (2) I don't want to create new projects in IntelliJ for each of the very small programs in the book (so far I've completed over 200, and most are fewer than 50 lines).

Recently, I was working on an example in Chapter 9, where the objective is to practice implementing interfaces. To do so, you first create an interface `Callback`, then a class `Client` that implements the interface, and then finally a class `TestIface` that both declares an interface reference variable and calls the method from `Client` on it. 

Working in Vim, I first placed all of these components (one interface and two classes) into a single file. It compiled and ran just fine.

The next lesson adds two more classes: one, `AnotherClient`, that implements the interface differently, and another, `TestIface2`, that calls the method from `AnotherClient` on an interface reference variable. So that I could minimize retyping, I opted to place each component in a separate file: one for the interface, and four for the respective classes.

The file containing the interface compiled just fine, but the classes were giving me guff:

{% highlight java %}
Client.java:3: error: cannot find symbol
class Client implements Callback {
                        ^
  symbol: class Callback
{% endhighlight %}

I was completely baffled. How could it be that `javac Callback.java` would work just fine, but `javac Client.java` would produce the error above? I could see that `Callback.class` did indeed reside in the exact same package directory as `Client.java`, but the compiler wasn't finding it.

I opted to try it out in IntelliJ. While I don't want to rely on the IDE as a shortcut, I was hoping that I could learn something by seeing how it fixed the problem. But this made things even more confusing - the *exact same* source code compiled in IntelliJ without a hitch. 

I fooled around for awhile (reading through examples of similar problems on [stackoverflow][stack_overflow], using `import` in myriad different ways, changing the names of the interface and classes, etc) trying to find an answer before I stumbled upon something that worked:

{% highlight java %}
> cd ..
> javac MyPack/Client.java
{% endhighlight %}

It turns out that in order to compile a class that implements an interface and resides within a package, you have to back up out of that package and compile by providing the file directory that includes the package. 

Who knew?

[java_reference]: http://www.amazon.com/Java-Complete-Reference-Ninth-Edition-ebook/dp/B00HSO0X6C
[intellij]: https://www.jetbrains.com/idea/
[stack_overflow]: http://stackoverflow.com/questions/16102893/cannot-find-symbol-saying-implemented-interface-not-found