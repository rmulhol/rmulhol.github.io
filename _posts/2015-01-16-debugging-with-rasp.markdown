---
layout: post
title:  "Debugging With RASP: Don't Forget to Check Your Work!"
date:   2015-01-16
categories: Ruby
tags: rails rasp
--- 

Recently, I encountered a stubborn error message. 

I've been working through [Agile Web Development With Rails][agile_dev], and I got to the point where I'm instructed to verify that the order information gathered by my depot application is being written to the database (chapter 12 - Task G: Check Out!). Simple enough, I thought, I'll just enter `sqlite3 -line db.development.sqlite3` and run `select * from orders;`.

To my dismay, my simple command was met with a daunting error message: `Error: no such table: orders`. 

How could this be? Had I failed to run `rake db:migrate` at some point? No, that wasn't it; I ran `rake db:rollback` and `rake db:migrate` several times just to be sure of that. Did my `database.yml` file contain errors? No, not that I could see; everything looked fine. 

Unsure of where to locate my bug, I decided it was time to go through the trusty RASP progression.

For those who are unfamiliar, RASP instructs you to:

**R**ead the error message

**A**sk a colleague an informed question

**S**earch using google, stackoverflow, and so forth

**P**ost on stackoverflow, online forums, and so forth

I had read the error message. The table `orders` was clearly not being found in the database under inspection. But why?

I asked a colleague about the error, but they too were befuddled after a quick review of my `db/migrate` folder and the `database.yml` file. 

It was time to begin pursuing an answer online. Google had helped me to correct several errors in the past, so there was no reason this should be any different. If I played around with a few search term combinations, I figured, an answer would reveal itself.

But that was not to be so. Google yielded surprisingly few results for every set of search terms I could come up with. There were several stackoverflow posts about `no such table` errors, but none that resembled my problem.

I was very close to posting online, but I had some hesitation. If this problem was common enough that others had encountered it and knew how to solve it, shouldn't there be posts about the issue already? I decided to take one final peak under the hood.

Upon closer inspection, I noticed that my app root directory now contained an empty `db.development.sqlite3` file. Where did that come from? I thought my development database was stored as `development.sqlite3` in my `db` folder (indeed, it was).

The source of my problem, it turns out, was a typo. Instead of typing `sqlite3 -line db.development.sqlite3`, I needed to be typing `sqlite3 -line db/development.sqlite3` (note the forward slash, rather than the dot, following db). When I corrected the typo and tried again, everything worked fine.

So, I propose an addition to the RASP cycle: **C**heck your work. We can call it CRASP.

Sure, most people already check their work before searching online. Sometimes an error message will point you right at the typo so that you can fix it without further effort. Sometimes you'll realize your own error upon a second glance at what you've written.

But other times, you'll miss the typo. You'll make it deeper into the RASP cycle before going back to locate what should have been a quick fix. 

In those instances when you find yourself deep into the google with no solutions in sight, go back and check your work one more time. What I've found is that as the solution to your problem begins to seem more and more elusive, it becomes more and more likely that it's closer than you think. If you can't find information about your error online, it's almost certainly a simple error you've made. 

So, join me in adding an addendum to the RASP process. Check your work before you go search online, and then check it again with closer scrutiny if you can't find an answer. Embrace CRASP.

[agile_dev]: http://www.amazon.com/Agile-Development-Rails-Facets-Ruby/dp/1937785564