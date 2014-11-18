{mainmatter}

# Understanding commits in Git

Ever wondered what a Git commit looks like internally? Why it has those long revision identifiers? If they are unique or not? We are going to look at exactly that!

## What's up with those long revision names?

One of the first things people notice when they switch from other version controls to Git is that revisions use hard to remember 40 char long hashes. Why do that if you can just use revision names such as `r4711`, `r4712` etc? The short answer: Git has integrity. 

If you and me both look at revision `f4f78b319c308600eab015a5d6529add21660dc1` on our machines and Git tells us that we have a clean working directory, we can be 100% sure that we are looking at exactly the same files. There is no way someone could manipulate a single bit without Git knowing about it.

This is especially important in an distributed version control system. There's no chance that bits could go lost in transit without Git noticing it.

## Introducing SHA-1 

Those 40 character revision identifiers are actually SHA-1 hashes. So what is a SHA-1 hash? SHA-1 is an algorithm and what it does is: it takes some data as input and generates a unique 40 character string from it. So what does unique mean in that context? Unique means that no other input data should ever produce the same hash. The same input data however should always produce exactly the same hash.

If you are on a UNIX system, try this out.

`echo -n "thoughtram" | openssl sha1`

This should print out `a9eb85ea214a6cfa6882f4be041d5cce7bee3e45` no matter if you run it on your machine or I on mine. It's the SHA-1 representation of the string `thoughtram`.

## Hashes over hashes

Now that we know what a hash is, we find us wondering, what exactly is the input data that Git uses to get to the commit hash? Let's start with a very abstract idea and iterate on it until we have a strong understanding of Git's internal commit representation. In principle it looks like this in pseudo code.

    sha1(
        meta data
            commit message
            commiter
            commit date
            author
            authoring date
        Hash-Of-Entire-Working-Directory (bear, with me!)
    )

It's a SHA-1 hash over a bunch of meta data (commit message etc) and the hash of the entire snapshot of the working directory with all it's files and folders. Let that sink in for a second. It's a hash over some meta data and another hash. So what exactly is this other hash that we hash again?

## Meet the tree object

There are four types of objects in Git's internal storage. Commit objects, annotated tag objects, blobs and tree objects. In order to fully understand Git's internal representation of commits, we first need to understand tree objects.

When I said `Hash-Of-Entire-Working-Directory` I made a bit of an oversimplification. Yes it's true, it's a hash over the entire working directory with all it's files and folders excluding ignored files. But at the same time it's not quite *that* simple. Let's assume the following project structure and then examine how it is represented with the corresponding tree object.

    .
    ├── .git (contents left out)
    ├── assets
    |   ├── logo.png
    |   └── app.css
    └── app.js

Here is what Git's representation of the working directory looks like.

![tree](images/git_tree.png)

Each file is stored as a blob object. So what does that mean? Let's keep things simple and just assume that Git generates the SHA-1 hash of the contents of `logo.png` and puts it somewhere addressable as `aa1b2fb696a831c89c53f787e03d863691d2b671`. It also generates the SHA-1 hash of the contents of `app.css` and puts it somewhere addressable as `4c511f16ef2644854d04cabebfcecc82be0eb04f`. Same goes for the `app.js` file. Notice that at this point Git doesn't even link the file name `logo.png` with the hash `aa1b2fb696a831c89c53f787e03d863691d2b671`. This is done on a higher level for good reasons.

Both `logo.png` and `app.css` are in the same directory: `assets`. And this directory, `assets` is represented as a tree object. So what is the tree object? The tree object works like a dictionary. It maps names to SHA-1 hashes. Behind those hashes there may be simple files (represented as blobs) or other tree objects.

In our example, it maps the name `logo.png` to the hash `aa1b2fb696a831c89c53f787e03d863691d2b671` and the name `app.css` to the name `4c511f16ef2644854d04cabebfcecc82be0eb04f`. Eventually the dictionary (the tree object) itself is also hashed and represented by the SHA-1 hash `7cf2a17f3345635d59e063cffddd23573b6e4a75`.

We aren't done yet. Both the `assets` folder and the `app.js` file are in the same directory. It's the top level directory and again it works like a dictionary. It maps the name `assets` to the hash `7cf2a17f3345635d59e063cffddd23573b6e4a75` (the child tree object!) and the name `app.js` to the hash `29bfcf9fa5824331081b31f0c307806c6f6b6f06`. And then again the dictionary itself is also hashed and becomes the root tree object with hash `9c435a86e664be00db0d973e981425e4a3ef3f8d`.

Let that sink in again, `9c435a86e664be00db0d973e981425e4a3ef3f8d` is the hash of the root tree object which essentially translates into a dictionary that maps names to hashes and those hashes can refer to blobs (for files) or other tree objects which in turn are dictionaries themselves that map names to hashes which can refer to...ah well, you get the idea.

## The commit object

Let's go back to our commit object from above and fill it with life. Let's assume we freshly started the repository and this is our initial commit. What follows is again pseudo code to demonstrate what exactly is used to get to the commit hash.

    sha1(
        commit message  => "initial commit"
        commiter        => Christoph Burgdorf <christoph.burgdorf@gmail.com>
        commit date     => Sat Nov 8 10:56:57 2014 +0100
        author          => Christoph Burgdorf <christoph.burgdorf@gmail.com>
        author date     => Sat Nov 8 10:56:57 2014 +0100
        tree            => 9c435a86e664be00db0d973e981425e4a3ef3f8d
    )

It's all the meta data plus the hash of the root tree object. And of course Git creates a SHA-1 hash from those contents. The commit hash.

Remember that I said Git has integrity? You can't change a single thing about this commit without getting a different SHA-1 commit hash. Want to change the commit message? The commit message is part of the content that is used to produce the SHA-1 hash, changing it will change the commit hash. What if we just add a whitespace somewhere in `app.css`? Doesn't matter. The SHA-1 hash of `app.css` won't be `4c511f16ef2644854d04cabebfcecc82be0eb04f` anymore and that in turn will cause the `assets` tree object to not be `7cf2a17f3345635d59e063cffddd23573b6e4a75` anymore and that in turn will cause the root tree object to not be `9c435a86e664be00db0d973e981425e4a3ef3f8d` anymore. That's integrity.

## Recap

In this post, we explored what Git's long revision numbers are about and how they provided integrity using a solid cryptographic approach.

Please note that I simplified some parts intentionally in order to reduce the noise and make the essence easier to grasp. If you like to dig deeper, I highly recommend to read up on the [Git's internals](http://git-scm.com/book/en/v2/Git-Internals-Git-Objects).

This post is part of a future blog series about Git internals and will also be part of our book [rebase - the complete guide on rebasing in git](https://leanpub.com/rebase-the-complete-guide-on-rebasing-in-git).