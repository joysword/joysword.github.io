---
layout: post
title: "[LeetCode] Search Insert Position"
date: 2014-09-10 23:18
category: LeetCode
tags: LeetCode algorithm interview
---

### Description
> Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.
>
> You may assume no duplicates in the array.
>
> Here are few examples.
>
> `[1,3,5,6]`, 5 → 2
> 
> `[1,3,5,6]`, 2 → 1
> 
> `[1,3,5,6]`, 7 → 4
> 
> `[1,3,5,6]`, 0 → 0

### Solution

[Search Insert Position](https://oj.leetcode.com/problems/search-insert-position/) is very easy, but the \\(O(log(n))\\) solution requires some attention to avoid bugs.

#### Simple solution : O(n)
A naive solution would be traverse the array, starting from index `\(0\)`, as long as the target value is larger than value in current index, move index to right by 1.

using `` `\\( and \\)` ``

`\(\forall x \in X, \quad \exists y \leq \epsilon\)`

using `\\( and \\)`

\\(\forall x \in X, \quad \exists y \leq \epsilon\\)




