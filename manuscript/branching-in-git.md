# Branching in Git

In the last chapter we learned the anatomy of a commit in Git. In order to understand what a rebase is, we first need to learn about the concepts of branches in Git. This chapter discusses how branches in Git are technically represented and how simple and yet powerful they are.

## Understanding Branches

When we create commit objects, each commit usually points to one or more parent commits. With each commit pointing to it's parent commit, Git is not only able to track the entire project history, it can also reconstruct any state of the project that happened during the development with the help of branches. A branch basically tells us, in which development branch we're currently working in, since there can be many, but technically, it's just a pointer at a commit.

To get a better picture of this, let's take a look at the following simple commit history.

![A simple commit history](/images/git-branching-commits-abstract-2.svg)

The figure shows a very abstract representation of a commit history that has just three commits with, of course, each commit having it's own SHA-1 hash. Let's assume our entire repository history is made up only from those three commits. There's a pointer called `master` pointing at commit `a5c3eb`. Guess what, this pointer is a branch. The `master` branch is the default branch that Git already created for us when we initialized our repository.

As mentioned earlier, a branch is really just a pointer pointing at a specific commit. In fact, we can inspect the file system and verify exactly that. Simply open the file `.git/refs/heads/master` and take a look at it's contents. Don't worry about the file structure for now. We'll talk about this part of the `.git` folder later. We can see that the file really just contains a SHA that points at a commit object in our repository. Another interesting fact: A branch is a movable pointer. Whenever we make a new commit, the branch points at that particular commit. Let's learn how we can create new branches.

I> A branch is a movable pointer that points at a specific commit.

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

This is why branches in Git are so super cheap. It doesn't copy the whole working directory in a new branch folder. Git really just creates a file with the branch name containing the commit SHA to point at and a `HEAD` file that points at the currently checked out branch. If we switch to another branch, what happens is that Git changes the content of `HEAD`. No more black magic involved.

We mentioned earlier, that a branch is in fact a movable pointer. By "movable" we mean that it automatically updates itself and points at the next commit, in case we're making one. To make this a bit more clear, let's assume we made yet another commit in our `feature` branch. Here's what our graph will them look like:

![Adding a new commit to feature](/images/git-branching-head-pointer-3.svg)

We created a new commit `c57e22` and the `feature` pointer moved on to it. `master` is still pointing at the other commit, simply because `feature` is the currently checked out branch. Let's switch back to `master` by running `git checkout master`.

![Switching back to master](/images/git-branching-head-pointer-4.svg)

`HEAD` now points to `master` again and the state of the entire working directory has been changed to what it was at `a5c3eb`. The next interesting thing to take a look at is how branches behave when they diverge. We jumped back to the state of where `master` is, but what happens when we make a new commit from here? Let's just modify some files and find it out. Our commit graph now looks something like this.

![Diverged branches](/images/git-branching-head-pointer-5.svg)

A new commit `3aa2ff` appeared and `master` moved on to it. At this point our history has diverged. We now have a parallel evolutions of our code base and can simply jump back and forth between them with the power of branches.

## Summary

In this chapter we learned that branches are just simple pointers that point at a specific commit SHA. We also learned about what the `HEAD` in Git is and that it simply points to the branch that is currently checked out. We created new branches and made them even diverged. At this point you may be wondering how to *merge* both ends back together. This is exactly what we will detail in our next chapter on three-way and fast-forward merges.
