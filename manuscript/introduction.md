# Introduction

The invention of Git was by all means one the best things that happened to the software industry in the past ten years. It's not that we haven't had version control before. In fact, most of us were happily using SVN or other version control systems before we made the switch. We didn't know how much better things could get. 

On the other hand, I've never met anyone who wanted to go back to SVN after he grokked the full power of Git. Git has features that you'll learn to love after you employed them in your daily workflow but didn't even know they exist before.

This book is about Git's most powerful feature: Rebasing. If you now think: *"I know what rebasing is. Why writ an entire book on it?"*, read on. You'll be surprised how much there is about rebasing that you probably don't know.

## Who This Book is For?

This book is intended as a guide for people already familiar with Git. While a deep understanding of Git isn't necessary to use this book, you would leave out many more basic features of Git, if this is your first Git book. If you don't feel familiar with the basics of Git we recommend reading the free [Pro Git Book](http://git-scm.com/book) before you read on.

## Overview

**Chapter 1: Understanding Commits in Git** gives you a quick introduction to what commits in Git actually are and what their anatomy looks like. This is fundamental knowledge in order to understand merging and rebasing in future chapters.

**Chapter 2: Branching in Git** makes you understand how branching works in Git and how they are handled under the hood. This knowledge is critical in order to understand merging and rebasing in future chapters.

**Chapter 3: Three-way and Fast-Forward Merges** details what merges are and what types of merges exist. It also discusses when a certain type of merge happens when doing a merge.

**Chapter 4: The Problem with Basic Merging** discusses arguments that generally speak against basic merging and why it makes sense to avoid them in certain environments.

**Chapter 5: Basic Rebasing** introduces the basic rebasing feature of Git. It details what a rebase is and why it is so powerful.

**Chapter 6: Interactive Rebasing** introduces interactive rebasing as the next level of rebasing in Git. Interactive rebasing lets you reorder, squash and split commits and turns out to be one of the most powerful features.

**Chapter 7: Reordering Commits** details how to reorder commits of an existing Git commit history.

**Chapter 8: Squashing Commits** discusses how to squash existing work-in-progress in order to generate a clean Git commit history.

**Chapter 9: Auto-Squashing Commits** gives you details on how to automate the squashing process when rebaseing in Git.

**Chapter 10: Splitting Up Existing Commits** details how to go back in time and split up existing commits of an existing commit history.

**Chapter 11: Executing Programs during Interactive Rebases** discusses how to execute programs when rebasing interactively. This is a feature that is not very well known but quite powerful when it comes to automating tasks during rebases.

**Chapter 12: Generating Changelogs with Clog** introduces `clog` as a platform independent command line tool to generate nicely readable changelogs based on a Git repositorys commit history.

## Help and Support

You can file issues, suggest changes, and open pull requests against this book by visiting: [http://github.com/thoughtram/rebase-book/](http://github.com/thoughtram/rebase-book)
