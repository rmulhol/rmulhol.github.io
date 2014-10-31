---
layout: post
title:  "Figuring Out Code: A Comment on Comments"
date:   2014-10-30
categories: Java
tags: general
--- 

When you begin learning a programming language, one of the first things you come across is how to create comments - blocks of text that explain what surrounding code does but are not processed when the program runs. 

People teaching you want you to know about comments so that they can provide you with examples of working code that are self-explanatory, and you want to know about comments for a variety of reasons: they help *you* write self-explanatory code, they let you run different variations of the same program without having to completely delete text you want to remove temporarily, they allow you to pair pseudo-code with real code, and so forth. 

One of the things I've learned, however, is that comments are frowned upon by some in the coding community. A mentor of mine, for example, once told me that "comments are where understanding goes to die." Another colleage published [a plea to CS teachers][plea] begging instructors to abandon draconian requirements that students write comments for every line of code. Clearly, not all programmers are on board with the idea that comments ought to be your go-to tool for writing readable code.

Where does this sentiment come from? What's wrong with comments intended to help you understand code?

Well, the answer is twofold:

(1) Comments often don't get updated as regularly as the source code they explain, so they can begin to actually work against understanding. If a comment says that a block of code does one thing when it's been upgraded to do something different, that comment can leave you scratching your head wondering how the code that says it does one thing can look like it does something very different (hint: *it does*). (Note: You could obviously be dilligent about updating your comments each time you update your code, but this is a sizable burden that many  do not carry throughout the life of a program. If only there were some alternative ...)

(2) There *is* a superior alternative. Most of the places where you'd insert a comment, you'd be much better off naming methods and variables in ways that are more transparent. Instead of naming your "Hello, world!" method `hw()`, you could name it `printHelloWorld()`. When your names leave little ambiguity as to what data or behaviors they contain, there's little need for explanatory comments *and* that lack of ambiguity is more likely to stand the test of time.

This seems like an open-and-shut case in favor of the parties who proclaim that we ought to use comments sparingly.

And yet, I recently had an experience with comments that led me to re-evaluate their utility.

The backstory is this: I've been working on writing a game of tic tac toe in Java, where one feature is an unbeatable AI player. To make my AI player unbeatable, I've been putting together a [minimax algorithm][minimax] to check the board and pick the optimal move. 

The premise of minimax is straightforward enough - examine every possible game state resulting from any given move up to a certain depth (in this case, the end of the game), score each of the moves accordingly, and then choose the move that minimizes the maximum loss (i.e. reduces the worst case scenario).

Implementing the minimax algorithm, however, is another story. There are a lot of moving pieces, and an error in one place can cascade throughout the entire program, tainting the results.

Late in the afternoon today, after working on my algorithm for a day and a half, I still couldn't get it working. The AI player would place moves in interesting patterns and even secure wins against sub-optimal play, but it wasn't properly avoiding moves that were all but certain to result in a loss.

The code I had looked right to me, and I had been tinkering with it for so long that I no longer knew what to change, so I opted to consult a mentor. But first, I thought, this could be a good time for comments. I didn't intend comments to make it into my final program, but I at least wanted to document what I thought every line of code in my program was doing before going and asking someone else to help decipher what was going wrong.

So, I set to work, meticulously commenting each line. Most things were easy to comment - I had been reading about minimax for so long, and looking at so many examples of pseudo and real code implementing the method that simply laying out and describing each step was a welcome form relief. 

But, wouldn't you know it, I found a few places where I had confused myself (what was I thinking when I put in *this* line of code?). So, I trimmed and edited, removing things I couldn't explain and adding things I needed to make my explanations coherent. And, after it all, I chose to give the program one final try.

*It worked*. **IT WORKED!!!**

I was ecstatic. Comments led me to articulate what I was doing and make sure that the output matched the intentions, and that's exactly what I needed to do to get things up and running.

I promptly deleted my comments, made sure spacing and indentation all looked pretty, and then pushed my project to Github. Happy times.

You may be above all of this. You may intuitively digest algorithms and output working examples without intermediaries. In that case, please kindly disregard.

But, if you're like me, and you find yourself struggling to implement something you understand conceptually and know should work, maybe give comments a try. You don't need to keep them. You don't need to tell others you used them. But articulating your thoughts can sometimes help you implement them, and comments helped me do that today.

[plea]: http://blog.8thlight.com/dariusz-pasciak/2014/08/13/an-appeal-to-cs-teachers.html
[minimax]: http://en.wikipedia.org/wiki/Minimax