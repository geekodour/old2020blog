---
title: "How to git squash quickly without messing up"
date: 2018-04-29T08:00:57+05:30
---

So you have a branch with few commits and you want to squash all of them into one
before sending a PR to your favorite opensource project.

Here's how you do it.

First do,

```text
$ git log
```
Scroll till you reach the upstream or origin branch commit where the current branch was branched from.
Then just copy the `commitid` of that commit. If you branched it from the master then it will either be
`(upstream/master, master)` or `(origin/master, master)`
i.e
```text
     * (HEAD of your branch)
    /
|--/ (you need the commit id of this)
|
|
(master)
```

Okay, now do (without "<>")
```text
$ git diff <COMMIT_ID> HEAD
```

This will show all the changes you made till now from the commit you merged, so if you `git squash` the these changes will be in one commit.

Now we have the `<COMMIT_ID>`, **let's git squash.**

```
$ git rebase -i <COMMIT_ID>
```
It will open your text editor(neovim for me) and will have something similar to this:

```text
pick 260ad1cdc a commit message (oldest commit)
pick fcbf580f0 commit message here
pick 239791001 another commit message
pick bd555ab92 yet another commit message (latest commit)
```
Now if you read the contents you'll get that, to squash you can either do `squash` or `fixup`.

When you do `fixup` it takes care of ignoring the commit messages of the commits you want to squash.

If you use `squash` then you have to remove the commit messages manually. (see below)

So to `squash` or `fixup` you just replace the 2nd line and subsequent lines with `squash` or `fixup`
**(the last line is your latest commit and the first line is the oldest commit)**,
but don't worry because git is smart enough to know that you want to squash all of them to one.

So, This
```text
pick 260ad1cdc a commit message
squash fcbf580f0
squash 239791001
squash bd555ab92
```
and
```text
pick 260ad1cdc a commit message
fixup fcbf580f0 commit message here
fixup 239791001 another commit message
fixup bd555ab92 yet another commit message
```
are the same.

Make the edit as mentioned above and save the file.  You're done.

You can verify your changes using `git log`

./ Happy Hacking :)
