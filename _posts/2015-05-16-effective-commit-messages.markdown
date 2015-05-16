---
layout: post
title:  "Making Version Control Useful: Effective Commit Messages"
date:   2015-05-16
categories: General
tags: git commits 
--- 

Over my time as a programmer, one area where I've felt there's always more to learn is the effective use of version control. Internalizing the basics of `git add`, `git commit`, and `git push` doesn't take too long, but there's so many more useful features that become relevant with time and experience.

Beyond additional features, there's also the challenge of utilizing the simple tools *effectively*. Just because you make regular commits during the development process doesn't mean you're setting yourself up to reap the benefits of version control in the event that you need it.

One of the most important foundations for effective use of version control useful is good commit messages. In the event you need to roll back, you'll be shaking your fist at yourself (or whoever is responsible for sloppy messages) if you can't identify which commit correlates to the state you want to reach.

In this post, I want to share some of the techniques I've learned for constructing helpful commit messages. There's a lot more to be said beyond the scope of what I'll cover here, so think of these suggestions as the basic foundation from which further improvements can proceed.

**Avoid Ambiguity: Deliver Details**

If I'm working on a project with poor test coverage, one priority is to add tests. As I wrap up tests for one class or namespace, it feels like a natural time to make a commit.

An earlier version of myself might add the message `improve test coverage`. It's concise and describes what I've done, so why not?

The trouble is that this commit message is too vague. Others looking at my commit history don't receive any useful information about how test coverage was improved.

At its most basic, a good commit message should provide a clear explanation for what changes have been made. That means that it needs to include a sufficient level of detail to be understandable without diving into the code.

A more helpful message might read `add tests for all methods on the Users controller`.

**DRY Commits: Reduce Repetition**

Imagine you're satisfied with the `improve test coverage` commit message above, and now you want to improve test coverage for the whole project. So you've got a series of commits, each sharing the same message.

This compounds the problems described above. Not only can you not tell what's happening in the first commit with the vague message, you're also facing a series of subsequent commits bearing the same problem.

Duplication among commit messages should be treated like duplication in code: something ripe for elimination. If you're finding yourself labeling multiple commits with the same message, chances are that you're not only being overly ambiguous about the changes being made in one instance - you're extending that ambiguity across your entire commit history.

**Being Honest: Don't Lie**

Sometimes while working on a new feature, you might find yourself revisiting some earlier code. Maybe you decide to refactor a few things.

When it comes time to commit, you clearly lay out the details of your new feature, giving yourself a pat on the back for putting together such a nice commit message. Push up all the changes, feeling good.

The trouble with this scenario is that there's some unrelated (and unclaimed) refactoring going on in the same commit. What if it broke something? Someone reading your commit history has no reason to believe this commit touched the breaking code.

If you're making changes unrelated to what's stated in your commit message, you're lying. The commit does something totally different than what's claimed in the message.

A good commit message should be honest. That means you can't always rely on the trusty `git add .`. Unrelated changes should be broken up into separate commits, so that each message tells the truth about what that commit is doing.

**Conclusion**

A good commit message begins with uniquely identifiable details that honestly describe exactly what changes are being made. There's a lot more you can do to make sure that you're being as helpful as possible, but sticking to these foundational premises will set you started on the track toward reaping value out of your version control.