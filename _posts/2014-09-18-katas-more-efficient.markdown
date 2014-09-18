---
layout: post
title:  "Getting More Efficient at Writing Quality Code: Doing Katas with Vim and Guard"
date:   2014-09-18 
categories: Katas
---

Learning to code requires a lot of practice, and one of my favorite ways to practice is by [completing code katas][performing_code_katas]. Doing katas helps you learn multiple things at once, since you're simultaneously writing quality code and developing the muscle memory to do it efficiently.

My first kata required me to develop a program that would find the [prime factors][prime_factors_kata] of any integer. Developing the program just once was a learning exercise, since I got to see how following the [three rules of test driven development][three_rules] yields good design. But the real challenge came when I had to grapple with time constraints; I was assigned to practice until I could complete it in seven minutes.

In soliciting advice about how to do so (the first run took me thirty minutes), one of the best tips I received was to check out some new tools: vim and guard. Though I initially felt very attached to my text editor and didn't see much trouble in manually running tests from the terminal, these tools have paid off tremendously - my last run wrapped up in 6 minutes. So, in this post, I want to lay out how I began to develop a familiarity with these tools and what kinds of benefits they've provided.

**Tool #1: Vim**

My first encounter with [vim][vim] was not pleasant. After innocently typing 'vim' into my terminal, I didn't even know how to get back out (the answer, by the way, is :q, for 'quit'). After that, I went back to coding in my text editor until I received a specific recommendation to give vim another try.

And, luckily, that advice came with a specific pointer: try typing 'vimtutor' into the terminal. This takes you into a self-guided tutorial where you deploy most of the basic vim commands and work through a vim program. And, as a bonus, it helps you set up formatting at the end (through the .vimrc file), so that vim begins to look and feel more like your text editor. [Want two space indents for programming in Ruby? [No problem][vim_indent].]

Beyond just providing another platform for writing code, what really makes vim worth the effort is the way it functionally nullifies the mouse. Once you're coding in vim, you're relying pretty much solely on the keyboard to move across and between files. You might not expect it, but this saves a ton of time over the course of writing a program.  

And the benefits continue to pay off as you learn and practice. Eventually, you stop reaching over to the arrow keys - h, j, k, and l work just fine, and they're right at your fingertips. You learn the commands for [moving around vim files][moving_around], and you come to develop an active distaste for anything that requires moving the mouse.

There are [numerous][vim_setup] [tips][learn_vim] out there on how to get started with vim. My central recommendation would just be to give it a try. You may be pleasantly surprised at the dividends it pays.

**Tool #2: Guard**

Unlike vim, [guard][guard] is a tool that is specifically suited to developing in Ruby (though I imagine there are equivalents in other languages). Its basic purpose is to monitor events in your files as you modify them. For me, its biggest utility has come in automatically running rspec tests. No more switching to another window to run all of my tests; now I see results immediately as I modify files. 

Guard's maintainers provide [some really helpful tips][guard_tips] for how to get everything up and running, and I've seen some helpful intro material from [others][other_guard] as well. Like vim, it might feel like a bit of a hassle at first, but the payoff is well worth the initial investment. Automating the process of testing has big returns when you're otherwise pausing to do so manually over the course of a kata, and that effect is magnified when you move on to writing larger programs.

I do want to mention that getting the most out of guard required me to remove the '--warning' option from my .rspec file. Before I did so, I was getting so many notifications that it was virtually impossible to see how my automatic test runs were turning out.

**Recap**

There are all sorts of skills you can develop to get more efficient at completing katas. Indeed, repetead iterations yield numerous dividends on their own. But if you're looking to get the most out of the experience, I would highly recommend seeking out all of the tools that are available to you. I've found vim and guard to be two really important examples of those tools, and I look forward to learning more.


[performing_code_katas]: http://blog.8thlight.com/micah-martin/2013/05/28/performing-code-katas.html
[prime_factors_kata]: http://butunclebob.com/ArticleS.UncleBob.ThePrimeFactorsKata
[three_rules]: http://butunclebob.com/ArticleS.UncleBob.TheThreeRulesOfTdd
[vim]: http://www.vim.org/
[vim_indent]: http://vim.wikia.com/wiki/Indenting_source_code
[moving_around]: http://vim.wikia.com/wiki/Moving_around
[vim_setup]: https://bentblog.herokuapp.com/entries/77
[learn_vim]: https://bentblog.herokuapp.com/entries/78
[guard]: https://github.com/guard/guard
[guard_tips]: http://guardgem.org/
[other_guard]: http://code.tutsplus.com/tutorials/testing-your-ruby-code-with-guard-rspec-pry--cms-19974