---
layout: post
date: 2014-02-11 15:00
title: "[LeetCode] Single Number"
category: LeetCode
tags: LeetCode algorithm interview
---

### Description
> Given an array of integers, every element appears twice except for one. Find that single one.

> **Note:**
> Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

### Solution
[Single Number](http://oj.leetcode.com/problems/single-number/) is with the highest AC rate in [leetcode](http://oj.leetcode.com). But I actually did not come to the solution straightly.

<!--more-->

The little trick did not come to me until I looked for it in the forum. If we know this trick, the problem is a piece of cake, but if we don't, this problem is difficult, if not impossible, to solve.

The little trick we are counting on is XOR. Basically it gets a 1 if two operands are different, 0 if they are the same. So we have:

```
0 ^ 0 = 0
0 ^ 1 = 1
1 ^ 1 = 0
1 ^ 0 = 1
```

If we do XOR for all the integers, all numbers that appear twice will cancel out, the final result will actually be the single number that we are looking for.


### Summary
So for this problem, the trick is XOR all numbers.

### More
What about all number appear once except one that appears twice?

Well, my first thought is that we can create an array to contain 1 or 0, `a[i] = 1` means `i` appears, `a[i]=0` means `i` has not appeared yet. If we find an integer `j` and `a[j]=1`, we know `j` is the number we want. But this only works when we know the maximum possible value in the array, and if it is very large, we will need huge memory space.

Another way to deal with it, in my opinion, is to use hash table. If `hash[j]` is not empty, it doesn't necessarily mean that `j` is existing, but we can just traverse bucket `j` to see if it is the case.