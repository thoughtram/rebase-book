# Branching in Git

In the last chapter we learned the anatomy of a commit in Git. In order to understand what a rebase is, we first need to learn about the concepts of branches in Git. This chapter discusses how branches in Git are technically represented and how simple and yet powerful they are.

## Understanding Branches

When we create commit objects, each commit usually points to one or more parent commits. With each commit pointing to it's parent commit, Git is not only able to track the entire project history, it can also reconstruct any state of the project that happened during the development with the help of branches. A branch basically tells us, in which development branch we're currently working in, since there can be many, but technically, it's just a pointer at a commit.

To get a better picture of this, let's take a look at the following simple commit history.

![A simple commit history](/images/git-branching-commits-abstract-2.svg)

The figure shows a very abstract representation of a commit history that has just three commits with, of course, each commit having it's own SHA-1 hash. Let's assume our entire repository history is made up only from those three commits. There's a pointer called `master` pointing at commit `a5c3eb`. Guess what, this pointer is a branch. The `master` branch is the default branch that Git already created for us when we initialized our repository.

As mentioned earlier, a branch is really just a pointer pointing at a specific commit. In fact, we can inspect the file system and verify exactly that. Simply open the file `.git/refs/heads/master` and take a look at it's contents. Don't worry about the file structure for now. We'll talk about this part of the `.git` folder later. We can see that the file really just contains a SHA that points at a commit object in our repository. Another interesting fact: A branch is a movable pointer. Whenever we make a new commit, the branch pointer points at that particular commit. Let's learn how we can create new branches.

## Creating Branches

Creating a branch in Git is pretty straight forward and very easy at the same time. All we need to do is to execute `git branch <branch-name>`. Let's create a branch called `feature` and see what actually happens. After running `git branch feature`, this is what our commit graph then looks like:

![Creating another branch](/images/git-branching-new-branch.svg)

Seen that? We just created another pointer called `feature` pointing at the exact same commit. Basically, all Git does, it creates a file called `feature` in `.git/refs/heads` with the contents of the SHA-1 hash of the commit. Now that we have two different branches pointing at the same commit, how does Git know which branch is currently checked out? This is where the `HEAD` pointer comes into play.

## Understanding HEAD

If you're familiar with other version control systems, you probably have a different understand of what the `HEAD` is. In Git, `HEAD` is a special pointer that simply points to at the currently checked out branch or commit. Coming back to our example, we're currently working in the `master` branch, which is why `HEAD` points at `master`.

![Understanding HEAD](/images/git-branching-head-pointer.svg)

And again, it's just a simple file inside the `.git` folder. The file called `HEAD` contains a string of the branch name that is currently checked out. We can verify it by simply switching to our `feature` branch that we've just created by running the following command.

{title="Switching to another branch",linenos=off,lang="sh"}
  git checkout feature

Opening `.git/HEAD` shows us that `HEAD` now points at `feature`, which points at our latest commit. Here's a graphical representation of what happened.

![HEAD pointing at feature branch](/images/git-branching-head-pointer-2.svg)

This is why branches in Git are so super cheap. It doesn't copy the whole working directoy in a new branch folder. Git really just creates a file with the branch name containing the commit SHA to point at and a `HEAD` file that points at the currently checked out branch. If we switch to another branch, it what happens is that Git changes the content of `HEAD`. No more black magic involved.

## Detached HEAD

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
