---
layout: page
title: MiniGit — Version Control from Scratch
description: A simplified, Git-like version control system built in C++ from first principles, using hand-implemented linked lists to track files and commit history.
img:
importance: 5
category: work
---

**MiniGit** is a small version control system written in C++ — built from scratch, with no
external libraries — that reimplements the core ideas behind Git: tracking files, snapshotting
their state in commits, and being able to check an earlier snapshot back out. It was my final
project for a Data Structures course, so the focus was on implementing the underlying data
structures by hand rather than reaching for the standard library.

## What it does

The program runs as a command-line tool with a simple menu of six operations:

- **Add file** — validates the file, copies its contents into a hidden `.minigit` directory under
  a version-tagged filename, and begins tracking it.
- **Remove file** — stops tracking a file.
- **Commit changes** — records a snapshot of the currently tracked files as a numbered commit.
- **Checkout** — reverts to an earlier commit by its commit number, restoring the files saved in
  `.minigit`.
- **Print list** — displays all currently tracked files.
- **Quit**.

## How it works — the data structures

The whole system is built on two linked-list structures implemented by hand:

- **Singly linked list** — a chain of `singlyNode`s, each holding a `fileName` and a
  `fileVersion`. This is the list of files currently being tracked, with `insert`, `searchList`,
  `deleteAtIndex`, and `printList` operations.
- **Doubly linked list** — a chain of `doublyNode`s representing commit history. Each commit node
  carries a `commitNumber` and its *own* head pointer into a singly linked list — so every commit
  owns a complete snapshot of the file list at that point in time. The `previous`/`next` pointers
  let history be traversed in both directions, which is what makes checkout-by-commit-number
  possible.

Snapshotting commits as a linked list of file-lists is essentially a hand-rolled, simplified
version of how a real VCS stores history — and building it this way made the trade-offs of each
data structure concrete.

## Tech stack

C++ (standard library only) · linked lists implemented from scratch · file I/O

## Links

- **Code:** [github.com/ksjacob27/Data_Structures_Final_Project](https://github.com/ksjacob27/Data_Structures_Final_Project)
