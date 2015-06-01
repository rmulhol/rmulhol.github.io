---
layout: post
title:  "Packaging Programs: Building JAR Files With IntelliJ"
date:   2015-06-01
categories: Java
tags: jar packages
--- 

As a software development apprentice, the latest challenge I've taken on is to build an HTTP server in Java. To verify that everything is working as expected, I need to run my program against the tests contained in [the Cob Spec Fitnesse suite][cob_spec].

A lot of work goes in to passing all of the tests, but the first step is simply giving Cob Spec access to my program. It's hard to run the tests if you can't access the source. For Cob Spec to access the source, the server needs to be packaged into [a JAR file][jar_info].

JAR stands for Java Archive; it's a format for grouping together all of the necessary Java classes plus any other relevant files into one artifact that contains a whole program. Beyond putting everything in one place, it also offers [numerous other benefits][jar_benefits].

So how does one create such a convenient file? When I started poking around, I found several resources that covered how you can [do so from the command line][jar_building]. But I was so used to automating these tasks from my IDE ([IntelliJ][intellij]) that I figured that there had to be a simpler way.

And, indeed there is. Creating a JAR file with IntelliJ requires nothing more than a few clicks and keystrokes.

**Building a JAR File in IntelliJ**

Once you've got a working Java program built in IntelliJ, packaging it into a JAR file is only a few steps away.

It begins by opening up the Project Structure menu. You can do so by clicking `File => Project Structure`, or with the keyboard shortcut `⌘;`.

Next, navigate to the `Artifacts` option on the left panel. This will open up a menu for building artifacts.

On that menu, clicking `+` prompts you to select what type of artifact you'd like to build.

After you select `JAR`, all that's left is to fill in the details - give your JAR a name, indicate where to find the `main` method, and specify what files to include.

Clicking `OK` in the bottom-right corner of the menu wraps everything up. You can now run `java -jar path/to/jar/filename.jar` on the command line to execute your program.

**Conclusion**

Creating and working with JAR files strikes me as one of those things that becomes virtually automatic after just a little bit of practice. But it can be a bit of a pain when you're just getting started. Luckily, as with so many other steps that go into working with Java, IntelliJ makes accomplishing this task quick and easy.

[cob_spec]: https://github.com/8thlight/cob_spec
[jar_info]: http://en.wikipedia.org/wiki/JAR_%28file_format%29
[jar_benefits]: https://docs.oracle.com/javase/tutorial/deployment/jar/
[jar_building]: https://docs.oracle.com/javase/tutorial/deployment/jar/build.html
[intellij]: https://www.jetbrains.com/idea/