# I want to view what files changed in a commit, what do I do?

So, you have been plugging away on your codebase, making all kinds of changes here and there. Suddenly, to your horror, you remember you overwrote some code a few commits ago that you actually need! *GASP* What do you do?

Luckily, `git` was made for those of us who are occasionaly... careless? There are a ton of mechanisms to help users see and reclaim old versions of the codebase.

First off, what do we do? Well, we need to see a list of the recent commits. That is pretty simple with `git log`.

Try it out in any repo, run `git log`... you'll see... You should see something like this:

```
commit 95083c3e050438f4a3480ad6489d1f358f94bbee
Author: Carson.Howard <myemail@email.com>
Date:   Wed Jun 7 13:26:55 2017 -0700

    Started doing some work on this repo
```

You may notice a couple of things:
 - `git log` returns a lot of log information, actually it returns _all_ of the log information
 - `git log` returns a lot of information on each commit. Some you may not need

Let's limit the output to be more manageable. To do this, run `git log --oneline` and you should see something more like:

```
95083c3 Started doing some work on this repo
```

Oh man, that's so much better. I dig it.

One last note, if you do not want to see all the commits you can limit the number of commits displayed by using the `-n` parameter. So, `git log -n 5` will show the 5 most recent commits.

Cool, we have this weird value `95083c3e050438f4a3480ad6489d1f358f94bbee` for the commit. But, what does that mean? Well, it is the [sha1 hash](https://en.wikipedia.org/wiki/SHA-1) of the commit contents. Commit contents include the commit message, a commit description, the author, the commit date, and some other stuff, but most importantly the `sha1` of the current tree at that time. This is cool because it means that the entire codebase at the time of the commit is stored in the commit's object somehow (not directly, but the tree is in there, trust me).

How do we get to the tree? Simple. There is a sweet `git cat-file` command that lets you see what the content of a specific `sha1` is. Try `git cat-file -p <commit_sha>` on a commit sha. When we run `git cat-file -p 95083c3e050438f4a3480ad6489d1f358f94bbee` we get:

```
tree 50366525b285aff952951b0b9202b05676e1de24
parent 9ebd11d2f9b6ac640258274365f3c0bb1b9043af
author Carson.Howard <myemail@email.com> 1496867215 -0700
committer Carson.Howard <myemail@email.com> 1496867215 -0700

Started doing some work on this repo
```

Look, we have a `tree` value with its own `sha1` of `50366525b285aff952951b0b9202b05676e1de24`. When we run `git cat-file -p 50366525b285aff952951b0b9202b05676e1de24` we get

```
100644 blob c510d28f6ba6fe2c7e4181c416213b20a400d7ed	.gitignore
040000 tree c7ff0b528463167e6e1742ad00a5a79f15e30b04	.vscode
100644 blob 5c57cef6c7dba0856a3de824fc4df614e3a7fbc4	LICENSE
100644 blob 44cfb7be8c2d24920616cd76d5cd99d5595be0f3	Makefile
100644 blob 1f264f13964821c9a7c7d456563bc5d0604023a2	README.md
100644 blob d1b67a1e59ce4c55a95011e05245eed807af3a58	kilo.c
100644 blob 5dd01c177f5d7d1be5346a5bc18a569a7410c2ef	test
```

Whoa, a ton of information! What an overload man... I'm like, freaking out. Don't worry, I will explain what we see.

We see a few things. For one, we see another `tree` item. That second tree represents the directory `.vscode`. If we ran `git cat-file -p c7ff0b528463167e6e1742ad00a5a79f15e30b04` we would see a similar structure for the files in that tree.

The other cool thing we see are `blob` objects. These are the literal content of the file the reference at that time. We can see what is in the blobs with, you guess it, `git cat-file -p`!

Let's run `git cat-file -p 44cfb7be8c2d24920616cd76d5cd99d5595be0f3` and see what was in the Makefile. We get

```
kilo: kilo.c
	$(CC) kilo.c -o kilo -Wall -Wextra -pedantic -std=c99
```

It's a simple Makefile, but it will do. That content is the literal content of the Makefile at the commit `95083c3e050438f4a3480ad6489d1f358f94bbee`. We managed to wander through the commit list and see what the repo looked like a while back. You can imagine this is a helplful activity, and indeed it is.

So, now you should be able to find anything in a repo, right? Mostly. I have faith that you can figure it out, though.

If you want to know more about `git cat-file` look [here](https://git-scm.com/docs/git-cat-file)
If you want to know more about `git log` look [here](https://git-scm.com/docs/git-log)

Thanks for reading!