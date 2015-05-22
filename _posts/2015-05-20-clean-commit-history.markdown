---
layout: post
title:  "To Change the Past: Cleaning Up Your Git Commit History"
date:   2015-05-20
categories: General
tags: git commits 
--- 

As I've been working to improve my Git etiquette, one thing I've been doing is revisiting my projects' histories and cleaning up their commits to be more modular and expressive. Having had a tendency to batch together collections of unrelated modifications under unhelpful commit messages, my work was cut out for me.

Fortunately, I was able to figure out some techniques for transforming Git's record of the past, and I'm happy with the results. Previously sloppy commits were renamed, broken up, and supplemented as necessary.

In this post, I'd like to share some of the techniques I found most helpful in rewriting Git history. If you're facing a similar struggle to my own, hopefully you don't need to suffer the same pain I did along the way.

A word of caution, though, before we begin: most of these techniques are best left aside if you're working in a context where other users actively depend on or contribute to your code. While rewriting your Git history can make it easier to grasp what you've done through previous iterations of your code, it can also make it more difficult to share and merge new contributions going forward if you're working with an outdated history. 

**From Whence it Begins: Revisiting the Past**

In order to accomplish any of the tasks that follow, it's necessary to be able to reach back to previous commits. I'll be discussing three primary strategies for doing so:

1) `git commit --amend` - to modify the most recent commit.

2) `git rebase -i HEAD~...` - to dive deeper into your project's history. Replace `...` with a number specifying how far back into your history you would like to reach.

3) `git rebase -i --root` - to adjust your project's initial commit.

**Control the Message: Renaming Commits**

Perhaps the most pressing task in constructing a solid commit history is simply writing good commit messages. I've [written previously][last_post] of some of the methods for doing so, but what if you want to retroactively map such techniques onto the poorly worded commits of your project's past?

This is probably the most simple error to fix. 

To reword your most recent commit message, simply execute `git commit --amend`. This will bring up a text editor with the previous message at the beginning of the document. Make your edits, save and close the document, and the log will reflect your changes.

To rename commits between the root and where you are currently, use `git rebase -i HEAD~...`, replacing `...` with how many commits back you'd like to reach. This will bring up a text editor listing all of the commits in the range between there and where you are now. Before each one, you'll see the word `pick`. Replace that with `reword` for the messages you'd like to change. Save and close the dcoument, and you'll see a document for each of those commits. Save and close those documents with revised messages, and the log will reflect your changes.

To rename the root commit message, run `git rebase -i --root`. This will take you make to the moment immediately following your initial commit, where `git commit --amend` will allow you to edit it as if it were your actual most recent commit. To complete this step, run `git rebase --continue`, which will bring you back to the end of your project's history[^1]. 

**Modular Code, Modular Commits: Changing Content**

Good for you if all you have to do is reword the commit messages, but chances are that bad commit messages are a canary in the coal mine for bad commit content. And by bad, I mean that the content of the commits exceeds or is incongruous with what's described in a well-worded commit message.

Luckily, making revisions here is within your reach.

If you need to adjust the content of your most recent commit, you can do so as if you were staging a brand new commit. Simply use `git add ...` and/or `git rm ...` (replacing `...` with the paths for files you'd like to add or remove) and then follow it up with `git commit --amend`. Saving and closing the document that pops up will revise the previous commit to reflect the changes.

If you need to alter content between the root and where you are currently, `git rebase -i HEAD~...` lets you do so. This time, for the commits you want to modify, replace `pick` with `edit`. You'll be taken back to the moment following the earliest commit you marked for edit, where you can make changes just like you were tackling your most recent commit (`git add ...`, `git rm ...`, and then `git commit --amend`).

After you've amended commits earlier in your project's history, you'll need to run `git rebase --continue` to proceed. If you only opted to edit one commit, this should bring you back to the future (the end of your project's history). Otherwise, it will roll you forward to the next commit marked for edit.

However, this is where things can get hairy. If you make revisions that conflict with later commits, git will stop in its tracks to ask you what to do. To deal with that, you'll need to edit the files in question as you see fit (you can see which files encountered a conflict as well as the specific lines in tension by running `git diff`), merge the changes (`git add ...`), and then proceed with `git rebase --continue`[^2].

To alter the content at the root, `git rebase -i --root` to get there, `git commit --amend` to edit, and `git rebase --continue` to proceed.

**Where Once There Was Nothing: Inserting New Commits Into Your Project's History**

Changing the content of a commit is all well and good until you find yourself wanting to remove content from a particular commit without deleting it entirely from your project's history. You know it doesn't belong in *this* commit, but you also don't want to tack it on at the end of your project's history when lots of intermediary commits assumed it was there. What to do?

For this one, I'm going to assume you're not just trying to add a commit after your most recent commit (hopefully you know how to do that if you've read this far).

The good news is, inserting a commit deep in your project's history isn't all that different.

Say you have an earlier commit that you want to break up into several separate commits so as to reflect a number of distinct changes. We know that we can use `git rebase -i HEAD~...` (or `git rebase -i --root`) to get there. We know you can strip out unwanted content and/or reword the message with `git commit --amend`. And we know how to make new commits.

Put all the pieces together, and you have your answer. Simply adding new commits (literally `git commit -m "..."`) before running `git rebase --continue` will cause those commits to live at an intermediary point in your project's history. You can break up a mammoth of a commit into a series of bite-size chunks without losing any of the files we want later commits to know about.

**Conclusion**

At first blush, editing your commit history can feel like a weighty endeavor. There's a lot to dig into, and ample room for error.

But making the necessary changes doesn't have to be a headache. Simply following the techniques above should get you where you need to go.

[^1]: As I discuss further down, you may need to help Git figure out how to merge any changes you make earlier in your history with conflicting changes that follow. This shouldn't be a problem here, though, if you're just rewording the commit messages.
[^2]: This is, in my experience, the most scary/difficult step to tackle. I'd recommend creating a test repo and practicing this step a few times.
[last_post]: {% post_url 2015-05-16-effective-commit-messages %}