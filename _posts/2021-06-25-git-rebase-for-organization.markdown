---
layout: post
title:  "Using Git Rebase for Branch Organization"
date:   2021-06-25
tags: git learning
---

I've recently started using `git rebase` pretty heavily in my work.
Rebasing is often discouraged, and with good reason; I won't
reiterate those reasons here.
However, I find it's extremely useful for certain uses, and solves some tricky
problems.

# Incomprehensible Git Histories

Have you ever been working on a branch, and your history is long and
incomprehensible?
If you're following the generally recommended practice of committing often, you
end up with lots of commits with messages that are meaningless to you the next
day.
When this happens, `git log` doesn't give you any _useful_ information.
Maybe you have a commit history like this:

```
6811bd081 Code feature A
0357f749f Typo fix on feature A
...a million tiny commits here...
62306ea02 Code feature B
9b53a4bca Bugfix on feature A
...a few million more commits...
bf3c3eb43 Totally unrelated change
689ac4e36 Integrate A and B
```

If you realize that you also need feature A on another branch, good luck cherry
picking it!

If you've always worked this way, you've probably learned that the Git history
is only good for linear changes:
rolling back the most recent change, or rewinding to a particular date.
I'd learned about features like revert and cherry pick, but I never found them
useful, because my commit histories have always been garbled.
If I have a branch with multiple updates, I can't easily cherry pick a single
commit into another branch, because the meaningful change is actually spread
across multiple non-contiguous commits.

But it turns out rebasing can do several really cool things for us:

* We can integrate changes from a mainline branch more usefully, without ending
  up with nasty merge commits in our feature branches.
* We can combine commits, getting rid of typo fix clutter.
* We can _reorder_ commits, so that related changes appear together in the
  history. Couple this with combining commits, and you can work some real magic.

# Always Be Rebasing

While I'm working, I am constantly committing.
This makes it easy to work because I can experiment without fearing that I'll
break something.
But the commit messages are useless.

I've found it is useful to rebase my branches frequently, so that I can revise my
commit messages while I still remember what I did and why.
If I let myself go even a couple hours without revising my commit history, I'll
usually have forgotten what most of my messages mean, and the log becomes
useless.

I squash and reorder my commits and break them down by intent.
If my commit history looks like the one above, I want to end up with something
more like this:

```
6811bd081 Feature A
62306ea02 Feature B
689ac4e36 Integrate A and B
bf3c3eb43 Totally unrelated change
```

In fact, I can probably get rid of the "Totally unrelated change" and just put
that on its own branch.

I like this because the commit history actually starts to tell a story.
I have a clear narrative of what I did and why.
My changes are divided into sections by their purpose.

Now if I'm working in some other branch, I can easily pull in Feature A if I
need to using `git cherry-pick 6811bd081`.
No weird merging or manually sifting through diffs needed.

This also means that my pull requests are easier to review and change.
Say the reviewers like feature B but hate A, and we want to just merge B.
No problem! I can get rid of the commits we want to throw out.

`git rebase -i master` is my best friend now. If I realize I made a typo 3
commits back, it's no problem!
I can just fix it, commit, and then during the rebase reorder the commits and
squash the typo fix into the commit I want to fix.
Think of that like `git commit --amend`, but altering history.
The commit log stays clean, and I get way more mileage out Git features.

# When Not to Rebase

Rebase _is_ problematic if you're collaborating.
If you're sharing a branch with multiple people, things get messy quickly when
you rebase, because each person is committing on top of different versions of
the same set of changes. Merge conflicts happen during rebases, and nobody's
happy. So only rebase when you're the only one writing to a branch.

However, rebase is a really useful tool when used wisely; your commits are clear
and focused, and you have a better record of what you're doing and why.
