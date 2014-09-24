---
layout: post
title:  "Getting Better Feedback: Start with a Readme"
date:   2014-09-23 
categories: Readme
---

The other day, I had an opportunity to get some feedback on one of my projects from a more experienced programmer. I was super excited. I always enjoy a good code review, and I felt like this project had gotten to the point where it could really benefit from a mentor's scrutiny.

So, we sat down, he cloned my repo, got set to run the tests, and then ... lots of error messages.

What could have gone wrong? I had run my program multiple times on my computer, and everything seemed fine then. Maybe something mysterious and disastrous had taken place in my project's passage between the local and remote repositories, and some of my data was corrupted?

No, the problem was much more mundane: I had written my project with Ruby 2.1.2 and Rspec 3.1.3, and the person checking it out was running earlier versions. His machine didn't recognize some of my syntax, so things were failing all over the place.

No big deal - versions get upgraded, tests get run, and we get down to the nitty gritty of my code. But, before we did that, this experience provoked a piece of advice I hadn't considered while tinkering in the minutiae of my project's implementation: I needed to be containing more information in my Readme.

I already knew that the Readme has value - why else would github prompt me to create one with every repo? But I didn't really have a good idea of what to include. I'd put in a small description of my project and leave it there, since a bunch of the details I'd find in Readmes for larger and more advanced projects didn't seem relevant to my little breakable toy.

But regardless of project size, it's useful to tell the user **what versions/gems they need** to make it run. Though someone might be able to wade through your code to try and deduce what all they'll need, it's much easier when this information is made available explicitly and upfront - and the Readme is the place to do that.

If users are going through the trouble to install and use the versions you've laid out, perhaps they should also know **how to run your program**. This sort of information can seem obvious after working on your project every day for weeks, but it isn't necessarily so clear to someone who's just getting aquainted.

A Readme is also good place to tell your users **how to run the tests**. Because of a problem I hope to overcome and write about soon, running "rspec individual_spec.rb" in my spec folder doesn't work; I have to run the tests by entering "rspec" (or "rspec spec") in the home directory. That's the sort of thing you might want to know if *you* want to run my tests.

And depending on your project's size and complexity, there are lots other factors to think about as well: **how to install**, **known issues**, and **ways to integrate with other tools** all come to mind as things your users might want to know. At the very least, you can always include a **basic description of the project**.

Ultimately, figuring out what exactly to put into a Readme is less important than just *thinking more* about that question. Doing so deliberately not only makes the project more accessible for others, it also forces you to get more familiar with it yourself. And, in doing so, you're not just creating some afterthought to your code; you're giving your code its proper introduction.

