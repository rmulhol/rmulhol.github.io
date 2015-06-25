---
layout: post
title:  "Connecting The Dots: Referencing Clojure Code in Java"
date:   2015-06-24
categories: Java Clojure
tags: jar interop
--- 

Now that I've got [a working HTTP server][java_server] written in Java, I've been trying to make it better by adding features. Specifically, I've spent the last few days on making it possible to play [my Clojure tic-tac-toe game][clojure-ttt] through the web interface hosted by the server. 

I wrote Clojure tic-tac-toe as a command line application, so there have been some hurdles in simply connecting all of the dots so that the game logic can support another interface. But perhaps the biggest hurdle has been figuring out the answer to a fairly straightforward question: how can I reference and use Clojure code from a Java application?

I didn't expect this to be too difficult. After all, Clojure is [hosted on the JVM][clojure_java] and provides access to Java's core libraries. Gaining access in the opposite direction wouldn't seem like much of a leap.

And while it wasn't *too* difficult, I was surprised at how little explanation I found out there about how to do so properly. Some sources are outdated or overly brief, and others go into great detail on parts of the process without rounding out the big picture.

Luckily, I was able to find some answers, and now making use of my Clojure code in the Java server is a breeze. So, for those who might be stumbling at a similar roadblock, here's what worked for me.

**Step 1: the Uberjar**

If you're putting together a Clojure project, you can't go wrong with [Leiningen][leiningen]. There are numerous reasons, but most pertinent for our current purposes is the simplicity with which it allows you to package a jar file.

Avid readers may recall that I wrote about how [Intellij makes it easy to package jar files][intellij_jar] a few weeks ago. Well, put me down as similarly impressed with Leiningen. To package a Leiningen project into a jar file, you execute one command: `lein uberjar`.

That being said, there are some things that can go wrong. While the step is easy to execute, it's the one I've found myself repeating most often as I overcome little obstacles along the way.[^1]

What can go wrong, you ask?

- Your `project.clj` file may not be properly configured to compile the jar. There are a number of things you need to get right, and they're not all immediately intuitive. While you can seek guidance from [the sample `project.clj` file at the Leiningen github repo][sample_project], that's 500 lines of code to parse.

- Your namespaces might not be properly set up in a way that's useful for your Java project. You need to provide instructions and Java-ready code to your namespaces if you want to be able to call Clojure functions from Java projects.

How do you address these stumbling blocks? Read on.

**Step 2: Fix Project.clj, Rinse, Repeat**

If you're not sure whether or not your jar file is functioning properly, the first thing you can do is to try and give it a run: `java -jar path/to/jar.jar`. If it doesn't execute, chances are you need to clean up your `project.clj` file.

The first thing to check is that you've properly designated a place to find your `main` function. My `project.clj` file has the line `:main tic-tac-toe.core`, which tells Leiningen to find my main function there. You'll need something similar for your jar to execute.

After that, you want to provide instructions as to which classes should be compiled ahead of time, rather than just in time. You can read about the differences between the two [here][aot_jit], but the core takeaway is that any namespaces which you intend to call as if they were Java classes should be compiled ahead of time. 

You can be sure that's taken care of with two steps. First, add `:uberjar {:aot :all}` to the `:profiles` map in your `project.clj`. Second, add `:aot [namespace(s)-to-compile]` as a separate line after your main method. These steps will assure Leiningen knows to compile everything you've included when you package the jar.[^2] 

After you're comfortable that your `project.clj` is in good shape, you'll want to give `lein uberjar` another run. If everything is working, great. If not, it's time to look at your namespaces.

**Step 3: Adjust Namespaces, Rinse, Repeat**

Even after Leiningen is good to go, you still need to make sure your relevant namespaces are ready to serve as Java classes.

The first concern is whether you've told them they might need to do that. You can do so by adding [`(gen-class)`][gen_class] to the top of each namespace you intend to call as if it were a Java class.[^3]

Second, you'll want to check whether the functions you intend to use in your Java project can be read as valid Java code. In my case, the `get-move` function on my AI player was causing problems in Java, so I added a wrapper function named `getMove`.

Third, there's the question of whether the data structures you're using play nicely with Java. Some of my Clojure functions work with vectors but need to be called with an ArrayList in Java, so I added conversions in my wrapper functions (e.g. `(ArrayList. (function (into [] arg))`).

Once you've taken care of these steps, hooray! It's time to start working with Clojure code in a Java project.

**Step 4: Get to Work**

After your `project.clj` is properly configured, you've edited your namespaces to play nicely with Java, and you've run `lein uberjar`, it's time to import your jar into your Java project.

At that point, you should be able to call Clojure functions as if they were Java methods (and as if your namespaces were Java classes).

It's slightly more tedious than calling built-in Java methods in a Clojure project, but nothing a little elbow grease can't help you overcome.

[^1]: While I put creating the jar file as step 1, it could just as easily fit in as the second-to-last step (right before you import and call it in your Java project), since creating it in a way that's useful for other programs is so error-prone.
[^2]: You should obviously feel free to be more judicious about using `:aot :all` if you don't need everything, but that line will make sure you miss nothing.
[^3]: You may need to pass arguments to `gen-class` to assure that the Clojure function come out as methods you can call as you wish in Java code.

[java_server]: https://github.com/rmulhol/httpServer
[clojure-ttt]: https://github.com/rmulhol/clojure-tic-tac-toe
[clojure_java]: http://clojure.org/jvm_hosted
[leiningen]: http://leiningen.org/
[gen_class]:  https://clojuredocs.org/clojure.core/gen-class
[aot_jit]: http://clojure.org/compilation
[sample_project]: https://github.com/technomancy/leiningen/blob/master/sample.project.clj
[intellij_jar]: {% post_url 2015-06-01-package-jar-intellij %}