---
layout: post
title:  "Testing Distributed Database Systems: Kyle Kingsbury's Jepsen"
date:   2015-05-28
categories: General
tags: distributed-systems databases
--- 

The other day, I had the privilege of attending Kyle Kingsbury's talk "Jepsen IV: Hope Springs Eternal" at the DevOps Chicago meetup group. The presentation was interesting, thought-provoking, and entertaining. I found myself doing all sorts of background research afterward just to make sure I was really digesting what he put out there.

In this post, I want to take a few moments to reflect on what I've learned. I can't promise anything more than my own interpretation of what he said (you can check out a version of the talk for yourself [here][jepsen_talk]), but hopefully this post can be of use to those who're also developing an interest in the topic. 

**What is Jepsen?**

Jepsen is a tool developed by Kingsbury for testing the performance of distributed systems undergoing network partitions. It's basically a generative testing framework for database systems. He's interested in measuring how well they preserve the consistency of their data when connections between nodes are interrupted.  

You sort of get a sense for where he's going right at the beginning of the talk, when he shows a slide portraying the structure that underlies APIs, and the databases are portrayed as a tire fire.

His research bears out that characterization. Even well reputed databases begin to show cracks under the pressure applied by Jepsen, as data becomes outdated, inaccurate, or lost altogether. 

This talk focused on [MongoDB][mongo], [Elasticsearch][elastic], and [Aerospike][aero], but you can see analysis for numerous other systems that have undergone the wrath of Jepsen at [Kingsbury's site][jepsen_blog].

**How Does It Work?**

You can read Kingsbury's introductory post on how Jepsen works [here][jepsen_intro], but the basic story is that it spins up a cluster of hosts to serve as a distributed database system, simulates a network partition, then sees how the system manages database operations under such conditions.

Kingsbury focuses on how well each database lives up to the claims made in its documentation. Given [the CAP theorem][cap] (and the [limitations of thinking in terms of that construct][cap_limits]), we might not expect these databases to offer perfect consistency. But we could expect them to live up to the claims put forward by their marketing departments.

Or so we thought. Kingsbury's findings instill a healthy dose of skepticism. He's found numerous places where databases do not live up to the claims made in their documentation, alongside several other bugs and instances of unexpected behavior. 

**So, What Now?**

It's already a community service to document the ways that widely-used or otherwise attractive software falls short of delivering on the features it claims to provide, but what else can we take away from a talk like this?

Perhaps the most important lesson Kingsbury delivered was simply to rely on measurement over intuition and guesswork. As with all the other forms of testing we use in software development, it's necessary to have a way to exercise our systems if we're going to be confident they behave as expected.

Another important point is that developers should put serious thought into what kind of consistency they need for their specific systems. There's usually a gap between the form of consistency you need and the "C" in CAP, and figuring out where that gap lies can enable you to make the optimal compromise in favor of other qualities like availability and performance.

Finally, Jepsen's research encourages those who build and maintain databases for distributed systems to stay vigilant. It's hard to claim that your system delivers certain promises when repeatable tests demonstrate that it does not, and it appears that Kingsbury has provoked some folks to update their documentation and/or squash bugs.

**Conclusion**

Having been poking around the literature on distributed systems for just a few weeks now, Kingsbury's talk was a really awesome way to start getting a real sense for how to test and work with such software. I can't recommend the talk highly enough for those who are interested in this domain. Thanks Kyle!

I look forward to learning more about his work and the surrounding research in the future, and I'll keep you posted on further developments as they role in.

[jepsen_talk]: http://www.ustream.tv/recorded/61443262
[mongo]: https://www.mongodb.org/
[elastic]: https://www.elastic.co/products/elasticsearch
[aero]: http://www.aerospike.com/
[jepsen_blog]: https://aphyr.com/tags/jepsen
[jepsen_intro]: https://aphyr.com/posts/281-call-me-maybe-carly-rae-jepsen-and-the-perils-of-network-partitions
[cap]: http://en.wikipedia.org/wiki/CAP_theorem
[cap_limits]: https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html