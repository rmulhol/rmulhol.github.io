---
layout: post
title:  "Process over Product: Learning From Refactoring"
date:   2015-07-05
categories: General
tags: refactoring tdd
--- 

Recently, I've been reading *Refactoring: Ruby Edition* by Jay Fields, Shane Harvie, Martin Fowler, and Kent Beck. It's been a whirlwind tour of the problems that provoke you to refactor and the solutions to those problems, as well as a really insightful look into the process of refactoring.

I had always thought of myself as refactoring whenever I made edits to my code. After all, I was trying to make it better.

But refactoring actually denotes a much more specific activity. According to the authors, refactoring is defined as "the process of changing a software system in such a way that it does not alter the external behavior of the code yet improves its internal structure" (xv).

Though it sounds somewhat simple, there's a lot to unpack. In what follows, I want to lay out the steps that go into not just editing code, but actually refactoring.

**Step 1: Write Tests**

A core tenet of refactoring is that you are not altering the external behavior of the code while you're making changes to its internal structure. An improvement to the code's internal structure means little if you're simultaneously breaking existing features.

In order to be sure you're not altering the external behavior of the code, it's super helpful to have tests. Tests provide quick and automated feedback as you're making changes, allowing you to refactor with confidence.

Refactoring is actually built into the three stages of test-driven development: red, green, refactor. Once you've defined some code's expected behavior and written the code to execute that behavior, you should also be refactoring to be sure your code is as clean and maintainable as possible. 

Even if you're not practicing test-driven development, you should write tests as you begin to refactor. That way, you can make sweeping changes to the internal structure of the code and be sure that you're not taking two steps back as you're taking one step forward. 

**Step 2: Identify Smells**

In order to add some rhyme and reason to your refactoring, it's necessary to ground your vision of how the code's internal structure could be improved in some sense of what's wrong with its existing organization.

The issues that provoke refactoring are described by the authors of *Refactoring* as "code smells": "certain structures in code that suggest (sometimes they scream for) the possibility of refactoring" (73).

This could be something simple like duplicated code or a long method, or something more complex like a refused bequest.[^1] The authors provide a helpful catalogue of code smells that set up a framework for analyzing and improving code by recognizing existing flaws.

It doesn't particularly matter which code smell you're trying to snuff out while refactoring, it just matters that you have some sense for what the code smell is. Proceeding to edit code without a clear sense of what error you're correcting leaves you prone to making edits that leave the code worse than when you found it.

**Step 3: Baby Steps**

As the authors lay out the various types of refactorings that can be carried out to correct the wide variety of different smells, they go into meticulous detail in laying out the steps for executing each one correctly.

Such details are neither circumlocutory nor superfluous; rather, they provide a roadmap for carrying out refactorings in a manner that's optimally productive and efficient.

One step that shows up many times along the way is running the tests. As you're refactoring, the tests should stay green throughout. If you let the tests go red for an extended period of time, the possibility arises that you may not be able to get back to green without undoing all of your adjustments.

Refactoring should proceed in small, incremental steps so that you're never far away from a good stopping point. Of course, you will likely continue to refactor throughout the life of your program. But doing so in baby steps assures that you can react quickly if you make a mistake.

**Step 4: Continue**

Though *Refactoring* provides an awesome catalogue of smells and refactorings, you're only really going to master the art of writing good code with practice and experience. Practice helps you carry out refactorings with ease, and experience gives you a sense for which smells are acceptable at the present juncture.

You may learn of new smells that cause you to carry out different refactorings. You may learn of new techniques that allow you to correct particular smells in a better way. You may even find yourself reversing previous refactorings down the line. 

All of this is fine and expected, and it's something that reflects the greater degree of knowledge you accumulate about how your program should be designed as you work with it over time. 

The key is to continue to refactor so that you're always leaving the code better than you found it.

**Conclusion**

I've found *Refactoring: Ruby Edition* to be a tremendously rewarding and worthwhile read. I'm excited to get more familiar with the smells and refactorings they lay out, as well as the process of doing refactoring the right way.

They only thing that's certain is that code I come across will warrant refactoring, and I'm excited to have gained some perspective into how to do so well.

[^1]: Subclasses that doe not need the methods or data of their parents.