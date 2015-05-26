---
layout: post
title:  "Laying the Foundation: On Extreme Programming Explained"
date:   2015-05-26
categories: General
tags: professionalism readings
--- 

I just finished Kent Beck's [*Extreme Programming Explained: Embrace Change, 2nd Edition*][xp_explained], and it was really striking. Not because it came out of left field, nor because it upended all that I had thought previously about what best practices underlie good software development. On the contrary, *XP Explained* was striking precisely because of how seemingly self-evident and irrefutable its propositions came across.

Who among us would dismiss the merits of pair programming? Do you oppose the idea that we ought to use tests to validate that our software does what we expect? How about quick iterations - would it be better to prolong our work cycles so that we only get feedback after months (or years) of potentially wasted effort?

No, of course not. For most of us, at least, I suspect that each of these inquiries would be met with a response in the negative. We place emphasis on shared collaboration, demonstrable results, and short feedback loops because we know they improve the quality of the software we deliver.

But how do we know this? Is what appears so self-evident and irrefutable really so? Perhaps not.

Having come across *XP Explained* over 15 years after its initial release, I came to realize that what I saw as self-evident was in fact only so due to this text's longstanding and far-reaching impact on contemporary literature. Extreme programming has so deeply imbricated itself into much of our daily workflow that we may not notice its profound wisdom, but it is there nonetheless.

Taking a look at other ways that software engineering could be conceptualized and carried out, one gains a sense for how transformative Beck's text has really been. And if you look close enough, you can even find places where its insights merit further exploration and application; there is still something there to offer today.

In this post, then, I'd like to take a moment to dive in and reflect upon some of the elements of *XP Explained* that I found most noteworthy. Though the book's wealth of suggestions eludes quick summary in a brief post like this, it is my hope that highlighting some of the book's key points can help us to carry forward its contributions here and now, in the spirit of continuous integration. 

**The Foundation: Values, Principles, and Practices**

One of the things that sets *XP Explained* apart from some of the more technical programming texts that I've picked up is that it starts on the ground floor, laying a foundation of values, principles, and practices that make it possible to develop software in ways that are more productive, efficient, and responsive to change.

Five core values form the bedrock for extreme programming: communication, simplicity, feedback, courage, and respect. These are the concepts around which we organize our judgments of specific habits and activities; those which act in their spirit are treated as good, while those that run against them are not. 

Principles enable us to build a bridge between abstract values and concrete practices; they put values in the context of our domain in order to yield guidelines that give shape to our day-to-day activities. When we think about how to promote communication, for example, we consider the principle of economics; do practices in the name of communication conform to our business needs?
 
Practices, finally, are the actual things we do as we work. Things like pair programming, test-driven development, and quick iterations are practices; they describe how we actually develop software in the vein of extreme programming. We choose which practices to make habitual based upon how well they conform to our principles and thereby actualize our values.

The values, principles, and practices of extreme programming serve as guideposts. They enable us to take the lessons of extreme programming and apply them in contingent circumstances. One may not be able to implement all of the guidelines that Beck offers, but one can come into harmony with the core insights of extreme programming by heeding its core foundation.

**What Else Could Be? Competing Visions**

What sorts of contingent circumstances might work against extreme programming? Can we envision plausible circumstances in which we might proceed otherwise?

In a manner that addresses these inquiries, Beck offers up the method of Taylorism. In this approach, one attempts to improve productivity by segmenting tasks into bite-sized chunks, optimizing the processes by which they are carried out, and then assembling them back together. This manner of engineering has been widely popular in various domains for many years.

While this process might seem to superficially resemble exteme programming, it also presupposes that work is best carried out through a divide-and-conquer approach. But this is not always the case. As Beck points out with respect to the Toyota Production System (TPS), sometimes it is better to allocate responsibility for the entire product to all workers. 

Extreme programming heeds the lesson of TPS, taking note of the particular social structure of work at play in software development. Rather than segmenting roles and responsibilities, it brings experts and stakeholders from various domains into the fold, trusting that collaboration will yield a whole that's greater than the sum of its parts.

This is not to say that there is not space for divide-and-conquer approaches in software development. Surely, that is not the case. The challenge, however, is to strike a balance, to adapt your process to your problem. Sometimes, divide-and-counquer will do. Sometimes, you must conquer-and-divide.

**Persisting Value(s): Continuously Integrating XP**

One of the more interesting sections of *XP Explained* came in Chapter 9: Corollary Practices. There, Beck discusses some of the practices of XP that can be very useful but also carry some risk.

Having a shared codebase, for example, suggests that any contributor should be able to contribute to any part of the codebase at any time. This yields the benefit that various parts of the system can be improved as they are encountered by disparate developers.

A problem arises, however, if there is a lack of shared responsibility and continuous integration. Left to its own devices, a shared codebase could devolve into a non-functioning heap of poor quality code where nobody claims responsibility for cleaning up the mess.

This suggests that extreme programming is something that you grow and nurture incrementally, much like the software you create while following its guidelines. Once a team has the trust and procedures in place to adopt a new practice, its adoption can yield a better workflow - even if doing so earlier might have been disastrous.

Such growth and evolution of extreme programming itself raises questions as to how else we can augment and extend the discipline. What ways can we continue to tinker with the ways that we develop software to deepen and advance XP's core values? I'll leave it to the reader to theorize such approaches for now, but it's a helpful and thought-provoking element of *XP Explained* that this door is left open.

**Conclusion**

On the final read, *XP Explained* is perhaps not quite so self-evident and irrefutable after all. Or perhaps it is, but only because we've learned the lessons from when it was not. Either way, the text provokes us to continue thinking about how we might carry on its legacy going forward.

We know that it begins from a set of values, principles, and practices that give form to our everyday activities. We have a template for jumping in, but we also have the flexibility to choose how to swim once we're in the pool.

If we accept the premise that continuous integration helps to improve our process for developing software, perhaps we might also explore the possibility that continuous integration ought to be applied to the process itself.

How we choose to do so is the challenge that lies ahead.

[xp_explained]: http://www.amazon.com/Extreme-Programming-Explained-Embrace-Edition/dp/0321278658