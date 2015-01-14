---
layout: post
title:  "Conundrums in TDD Land: Improving Elegance vs Reducing Dependencies"
date:   2014-10-26
categories: Java
tags: TDD
--- 

As I've been doing more work with Java, one of the things I've focused on is getting my Java TDD abilities up to where they are with Ruby. JUnit is a whole different beast from Rspec, and figuring out how to structure and write tests within this framework has been a new and interesting challenge.

One of the things I've focused on lately is making sure that my tests for one object don't depend on other objects. This isn't by itself a departure from the form of testing I've done in Ruby/Rspec - you always want tests of one object to work regardless of changes to other peripheral objects - but figuring out the implementation details has sometimes served to remind me of just how new to this language I still am.

For example, right now I'm working on a tic tac toe game. One component of the game is an AI player. This object is supposed to react to moves chosen by the human player so that it can choose the optimal move for itself. To do so, it looks at the current state of the board and evaluates how likely any given move is to maximize its chances of winning.

This isn't a particularly novel challenge for those who are familiar with building a tic tac toe game, but it does create a temptation to inject unwanted dependencies into my testing suite. Since my AI player needs to look at the board to figure out its next move, it seems easiest to inject an instance of the board class into my tests for the AI Player and then just work with that. 

Doing so, however, would result in unsatisfactory tests. The tests in my AI player could break due to unrelated changes in my board, and I wouldn't really know whether or not there was a problem with my AI player itself. The goal should be to make the tests on the AI player rely only on the logic within that object, and then to trust that messages sent to the board will work fine as long as they are sent.

To actualize this goal, I've learned that I generally want to be passing things like the board into my methods via parameters, rather than initializing them at the top as instance variables. When the board being evaluated is simply an argument received by a method, it's easy to simulate various configurations of that object for all sorts of different tests. If that board were an instance of the board class that was pre-determined at the top of the tests, then I'd be relying on the sort of dependencies I'm hoping to avoid.

One problem this has created for me, however, is that it prevents me from using shortcuts that I feel would make my code more elegant. Passing in arguments generally means creating methods with up to three or four parameters numerous times throughout my classes, where many of them could be set by default if I relied upon calls to instance variables. 

This isn't a huge problem; I'll take well tested code over elegant code any day. But, one of things I look forward to figuring out as I move ahead is how to have my cake and eat it too. There must be ways to write code that is well tested and elegant, and that's one of the things I'll be looking to learn as I continue my journey.