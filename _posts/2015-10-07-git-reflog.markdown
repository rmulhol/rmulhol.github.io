---
layout: post
title:  "Undoing An Erroneous Force Push: The Git Reflog"
date:   2015-10-07
categories: general
tags: git
---

Recently, I've been working on a project maintained by multiple developers. There are a decent number of pull requests hanging around, and a lot of work happening at all times. One of my roles has been to prepare open pull requests for a clean and easy merge.

Not too tricky of a task, most of the time. Rebase the branch onto master, resolve all the conflicts, push up the changes, and it's good to go.

Unless you mess up the rebase, incorrectly resolving merge conflicts. Running the tests will save you most of the time, but not always. And if you get too far, you may have just force pushed a broken branch onto the PR. Oops.

Luckily, this sort of error is not the end of the world. Not at all.

There are number of ways to deal with inadvertent force pushes; I'd recommend [this blog post][help] if you haven't come across it already. But in this case, a very simple solution is to take advantage of [the `reflog`][git_reflog].

If you're in your project's directory and you type `git reflog` on the command line, you'll see a history of actions you've taken within the project: moving between branches, making commits, etc.

If you pinpoint the [SHA][git_sha] corresponding to the place where everything went awry (e.g. the place right before where you began the faulty rebase), you can `git checkout` that SHA and begin again. Or just breathe a sigh of relief as you push it up un-catastrophically-rebased.

[help]: https://blog.8thlight.com/sandro-padin/2015/06/08/help-i-just-force-pushed-to-master.html
[git_reflog]: https://git-scm.com/docs/git-reflog
[git_sha]: https://git-scm.com/book/en/v2/Git-Tools-Revision-Selection
