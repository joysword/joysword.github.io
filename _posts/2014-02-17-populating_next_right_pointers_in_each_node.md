---
layout: post
title: "[LeetCode] Populating Next Right Pointers in Each Node"
date: 2014-02-17 13:00
category: LeetCode
tags: LeetCode algorithm interview
---

### Description
> Given a binary tree
>
>```
>    struct TreeLinkNode {
>      TreeLinkNode *left;
>      TreeLinkNode *right;
>      TreeLinkNode *next;
>    }
>```
>Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.
>
>Initially, all next pointers are set to NULL.
>
>Note:
>
>You may only use constant extra space.
>You may assume that it is a perfect binary tree (ie, all leaves are at the same level, and every parent has two children).
>For example,
>Given the following perfect binary tree,

>```
>         1
>       /  \
>      2    3
>     / \  / \
>    4  5  6  7
>```

>After calling your function, the tree should look like:

>```
>         1 -> NULL
>       /  \
>      2 -> 3 -> NULL
>     / \  / \
>    4->5->6->7 -> NULL
>```

### Solution

[Populating Next Right Pointers in Each Node](http://oj.leetcode.com/problems/populating-next-right-pointers-in-each-node/) is a very interesting problem. I did not came up with the algorihm until I went to [Discuss forum](http://oj.leetcode.com/discuss/oj/populating-next-right-pointers-in-each-node).

<!--more-->

It is clear how to link the left child to the right child of a node, but I did not figured out how to link the right child of a node to the left child of this node's sibling.

The solution is to use the links we create in previous steps. Below is the algorithm to solve this problem:

Traversing from root to the left-most leaf by moving to left child in every iteration. With in each iteration, traverse from left-most sibling to right-most one using links created in last iteration. In odd steps, link current node's left child to its right child; in even steps, link current node's right child to current node's next node's left child.

### Summary



