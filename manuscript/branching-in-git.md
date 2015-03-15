{mainmatter}

In this chapter we will take a deep dive into Git branches. We'll see how simple yet powerful the concept of branching in Git is. 

In the last chapter we took a detailed look on the anatomy of a Git commit. Now that we know how commits work in Git let's expand on our knowledge and understand branches. It's important to first grok the concept of branching in Git before we go into merging.

## Starting with a simple scenario

Let's start by looking at a simple commit history.

![A simple commit history](/images/git-branching-commits-abstract-2.svg)

Let's assume our entire repository history is made up only from those three commits. There's a pointer called `master` pointing at commit `a5c3eb`. That's all what branches are: they are movable pointers. Let's create a branch called `feature`.

{% highlight ruby %}
git branch feature
{% endhighlight %}

![Creating another branch](/images/git-branching-new-branch.svg)

Seen that? We just created another pointer called `feature` pointing at the exact same commit. Basically, all Git does it creates a file called `feature` with the contents being a 40 char string, the SHA-1 revision name of the commit.

But wait! Now that we have two different branches pointing to the same commit. How does Git know which branch is currently checked out? This is where the `HEAD` pointer comes into play!

![Understanding HEAD](/images/git-branching-head-pointer.svg)

The `HEAD` is a special pointer that simply points to the currently checked out *branch* or *commit*. And again, it's a simple file inside the `.git` folder called `HEAD` which in our case currently contains the string *master*.

Ok then, what happens if we switch to our feature branch?

{% highlight ruby %}
git checkout feature
{% endhighlight %}

![Switching branches](/images/git-branching-head-pointer-2.svg)

Exactly, all what happened is that `HEAD` is now pointing to `feature` instead of `master`. Switching between `master` and `feature` at this point boils down to Git replacing the string *master* with *feature* in the HEAD file. Super cheap!

But what happens if we now create or modify some files and make another commit? Let's find out.

{% highlight ruby %}
vim file.txt
git add file.txt
git commit -m "yay, that's fun!"
{% endhighlight %}

![Adding a new commit to feature](/images/git-branching-head-pointer-3.svg)

We created a new commit `c57e22` and the `feature` pointer moved on to it. That's because branches are simply movable pointers to commits. But why did `feature` move and `master` did not? Because `feature` is the currently checked out branch. And how does Git know? Because `HEAD` points to `feature`! Simple isn't it?

Let's switch back to `master`.

{% highlight ruby %}
git checkout master
{% endhighlight %}

![Switching back to master](/images/git-branching-head-pointer-4.svg)

What happened now is that the `HEAD` pointer changed to `master` and the state of the entire working directory was changed to what it was at `a5c3eb`.

Let's modify a file and create another commit.

{% highlight ruby %}
vim anotherFile.txt
git add anotherFile.txt
git commit -m "yay, more fun!"
{% endhighlight %}

![Adding a new commit to master](/images/git-branching-head-pointer-5.svg)

A new commit `3aa2ff` appeared and `master` moved on to it. At this point our history has diverged. And this is the whole point of branches. They enable us to have parallel evolution of a code base.

At this point you may be wondering how to *merge* both ends back together. This is exactly what we will look at in our next Git episode. Stay tuned!

## Detached HEAD

Ever came across a detached `HEAD`? Now that we know how Git handles branches, it's the perfect time to demystify the detached `HEAD`. You'll be suprised how simple it is. In fact, you may have spotted a hint in the text above.

>The `HEAD` is a special pointer that simply points to the currently checked out *branch* or *commit*

Not only can we check out branches, we can also checkout any commit revision explicitly. 

{% highlight ruby %}
git checkout 339aa3
{% endhighlight %}

![Checking out commits explicitly](/images/git-detached-head-2.svg)

You see? What happened now is that `HEAD` points to `339aa3` explicitly and the entire working directory was changed to what it looked like at that commit.

>When `HEAD` points to a commit hash explicitly (rather than to a branch) it's in a **detached** state.

What happens if we create new commits from here? Let's find out!

{% highlight ruby %}
vim someFile.txt
git add someFile.txt
git commit -m "detached HEAD fun"
{% endhighlight %}

![Adding commits to a detached HEAD](/images/git-detached-head-3.svg)

Let's add one more!

{% highlight ruby %}
vim someFile.txt
git add someFile.txt
git commit -m "more detached HEAD fun"
{% endhighlight %}

![Adding even more](/images/git-detached-head-4.svg)

We can go on like that. Does that mean, we don't actually need branches? Yes and no. What we just did is kinda like having a branch without a branch name. It works but there are two problems:

- How do you remember the SHA-1 key without a simple branch name?

- Commits that are not reachable by any branch or tag will be garbage collected and removed from the repository after 30 days.

No need to worry though. We can simply create a new branch right here!

{% highlight ruby %}
git checkout -b bugfix
{% endhighlight %}

![Giving it a name again](/images/git-detached-head-6.svg)

Bottom line: If in doubt, just branch. Branches are too lightweight to even think twice about them.
