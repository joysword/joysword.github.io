---
layout: post
title: "[LeetCode] Unique Binary Search Trees"
date: 2014-02-12 15:00
category: LeetCode
tags: LeetCode algorithm interview
---

### Description
> Given n, how many structurally unique BST's (binary search trees) that store values 1...n?

> For example,

> Given n = 3, there are a total of 5 unique BST's.

> ```
   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

### Solution
Before we start to solve [Unique Binary Search Trees]((http://oj.leetcode.com/problems/runique-binary-search-trees/), recall that a BST is a binary tree which has the following properties:

* The left sub-tree contains only nodes smaller than the root;
* The right sub-tree contains only nodes larger than the root;
* The left and right sub-tree each is a BST.

<!--more-->

If `n = 1`, the answer is 1.

If `n = 2`, the answer is 2, either 1 is root or 2 is root.

```
	1          2
	 \        /
	  2      1
```

If `n = 3`, the answer is 5. If 1 is root, we need to put 2 and 3 in the right sub-tree, and we'll have 2 BST's; if 2 is root, we need to put 1 and 3 in each side, respectively, and we'll have 1 BST; if 3 is root, we need to put 1 and 2 in the left sub-tree, and we'll have 2 BST's.

If `n = 4`, the answer is . If 1 is root, we need to put 2, 3 and 4 in the right sub-tree, and we'll have 5 BST's, just like when `n = 3`; If 2 is root, we need to put 1 in the left sub-tree and to put 3 and 4 in the right sub-tree, and we'll have `1*2=2` BST's; If 3 is root, we need to put 1 and 2 in the left sub-tree and to put 4 in the right sub-tree, and we'll also have `2*1=2` BST's; If 4 is root, we need to put 1, 2 and 3 in the left sub-tree, and we'll have 5 BST's, just like when `n = 3`.

...

Each time we put a number at root, we know how many nodes in each of left and right sub-tree, thus we know how many unique BST's can there be as left or right sub-tree.Multipling number of left sub-tree by number of right sub-tree will give us the answer for this root. Summing all different selections for root will give us the final result.

### Summary
The formula to calcute the number of unique BST's is `\(numTrees(n) = \sum_{i=1}^{n} numTrees(i) * numTrees(n-i-1)\)`

### More
I thought I might get `TLE` in my first attempt for solving it in a recursive way, but instead I got `Wrong Answer`. This was because I used `+` instead of `*` betweeen the number of left and right sub-trees.

The second attempt was `Accepted`.
