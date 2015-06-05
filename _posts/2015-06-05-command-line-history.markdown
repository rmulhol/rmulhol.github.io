---
layout: post
title:  "To Revisit the Past: Working with the Command Line"
date:   2015-06-05
categories: General
tags: command-line history
--- 

This week, I was tasked with putting together a presentation about the command line. Having stuck  to the most basic Unix operations (`cd`, `ls`, `mv`, etc.) throughout most of my time developing software, I had to do a bit a research.

Though I learned a lot of things preparing for the talk, perhaps the most useful tool I picked up is one that helps you reach into your command history quickly and easily: `CTRL-R`.

Typing `CTRL-R` in the command line opens up an interactive search line (with autocomplete) for matching previous commands with the bare minimum of its opening characters. Once you hit enter, it's immediately executed.

Of course, you can accomplish the same task with `!<str>` (replacing `<str>` with whichever opening characters you would search for), but `CTRL-R` gives you certainty that you'll get the right command right away, and the ability to execute it immediately[^1].

Having located this tool, I'm thinking it will never be necessary to use the up/down arrow keys for toggling through command line history ever again. Good riddance. Using CTRL-R allows faster retrieval with guaranteed precision.

Finding this tool has also sparked my interest in discovering more sophisticated Unix commands. I'm excited to find out what other operations I can perform to improve my efficiency and productivity as I continue to work with the command line.

[^1]: To get certainty, you can run `!<str>:p` (to see the command this search found) and then `!!` (to excecute) once you're sure it's right, but this inserts an extra step. 