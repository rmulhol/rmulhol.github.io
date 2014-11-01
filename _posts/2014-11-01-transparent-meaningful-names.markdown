---
layout: post
title:  "In the Interest of Transparency: Make Sure Your Meaningful Names Keep Up With Your Code"
date:   2014-11-01
categories: general
tags: variables methods
--- 

In chapter 2 of [Clean Code][clean_code], Uncle Bob Martin suggests that an important technique for writing quality code is using meaningful names. His rationale is sound. While it might seem like cryptic labels can make code appear more elegant and thus easier to take in, it's likely that those benefits comes at the expense of comprehensibility - as the code grows, it becomes increasingly difficult to understand how all of the various abbreviated identifiers fit together.

To operationalize this approach, I've been attempting to use meaningful names while constructing my tic tac toe game. My `getMove()` method, for example, has three parameters: the current state of the board, the move identifier for opponent, and the move identifier for the current player. Instead of writing the method as `getMove(x, y, z)`, it makes sense to label those parameters like so: `getMove(board, opponentMove, myMove)`.

Using these names for my parameters allows me to carry out all sorts of evaluations to determine which move is optimal while assuring that those evaluations are unambiguous. I can see whether placing `myMove` in one place on `board` will cause a win (taking it if so), try to block a loss if placing 'opponentMove` somewhere on `board` would cause that and a win is not available, check whether placing either move could cause a fork and take or block it accordingly, etc.

I proceeded through three AI players writing my method like this: a dumb player that returns random moves, a smart AI player that manually checks for wins, losses, forks, and the middle space, and an abstract AI player using the minimax algorithm to consider, evaluate, and score all possible moves. Everything worked perfectly.

However, I later added a configuration option. Instead of having the game run in default mode (3x3 board, human vs computer, 'X' v 'O'), I would now allow the user to construct custom versions of the game (variable board sizes, player identities, and move signatures). Things were still working well, with one caveat: the minimax AI player was not functioning properly when it was player 1.

This problem had me very confused. I spent a number of hours tinkering with my AI player trying to figure out where I had introduced some subtle error into my minimax algorith.

Unfortunately, the error lied elswhere. It turned out that my Runner class, when it was making the call to `getMove()`, sent the arguments `board, player1Move, player2Move`. Here, I had used meaningful names for my arguments, but they expected to be operating within the game's default configuration. Once I added the option to customize the game, `getMove()` was failing to provide correct moves when the AI player was player 1 because it was always receiving `player2Move` as `myMove`.

This error is entirely my own, rather than a fault with the practice of using meaningful names. Indeed, the third point in Uncle Bob's chapter on meaninful names is to avoid disinformation. What I learned, however, is that you must be dilligent with the meaningful names you choose so that the code you write actually expresses your intentions. Over-specifying right now might seem like it makes your names even more meaningful, but it comes at the cost of requiring abstraction when you abstract related features in other parts of your program.

[clean_code]: http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882