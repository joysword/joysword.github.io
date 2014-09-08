---
layout: post
title: "Remove Duplicates from Sorted List"
date: 2014-09-08 04:18
category: LeetCode
tags: LeetCode algorithm interview
---

### Description
> Given a sorted linked list, delete all duplicates such that each element appear only *once*.

> For example,
> Given `1->1->2`, return `1->2`.
> Given `1->1->2->3->3`, return `1->2->3`.

<!--more-->

### Solution
[Remove Duplicates from Sorted List](https://oj.leetcode.com/problems/remove-duplicates-from-sorted-list/) is not difficult, but require being careful to get `Accepted`.

The idea is straightforward: go through all nodes, if *next* node has the same element as *current* one, make current one connect to next of next node.

Only thing that needs to be kept in mind is to test if a pointer is `NULL` before accessing its `val` field or `next` field.

If you use two pointers, one for current node and one for next node, the code will be simpler. But if you want to use single pointer (and use its `next` field to point to next node), you need to pay more attention.

### Summary
Traversing the list and 'jump over' nodes that equal to previous one.

### More
My first *meaningful* attempt got `Runtime Error`. Of course this was due to not paying enough attention to `NULL` pointer.

Then I got `Wrong Answer` because my code didn't work correctly. I forgot to put `p = p->next;` into `else` block. After fixing this I got `Accepted`.


