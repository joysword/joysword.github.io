---
layout: post
title: "[LeetCode] Linked List Cycle"
date: 2014-02-17 07:00
category: LeetCode
tags: LeetCode algorithm interview
---

### Description
> Given a linked list, determine if it has a cycle in it.

> Follow up: Can you solve it without using extra space?

### Solution

[Linked List Cycle](http://oj.leetcode.com/problems/linked-list-cycle/) is a very interesting problem.

##### Simple solution

A simple and straightforward solution that I came up with is to traverse the linked list and put the address of each node into a set. Whenever we find a node already in the set, we meet a cycle.

<!--more-->

The complexity of this algorithm is `\(O(n*log(n))\)`, because to find an element in a node (`std::set.find(element)`), we need `\(O(log(n))\)` time.

##### Better solution
As we can see from the `Follow up` in **Description**, there is a solution in which we use no extra space.

This solution came from people in [discussion forum](http://oj.leetcode.com/discuss/122/does-everyone-solved-it-with-o-n-complexity), and we call it ` fast runner and slow runner` solution.

The general idea is to have two pointers trasversing the list in different speed. One moves one step in every iteration, while the other moves two steps.

The principle behind this algorithm is that if there is a cycle, the pointer who moves faster will eventually catch up with the pointer who moves slower. It is just like two athletes running in a track. Whoever runs faster will eventually catch up with the other one by running exact one lap more.

This solution uses only `\(O(n)\)` time and `\(O(1)\)` space.

### Summary
Using `fast runner and slow runner` strategy to see if faster runner catch up with slower runner

### More
My first attempt, in which I used **Simple solution** got `Compile Error` because I wrote this: `mySet.find(p) < mySet.end()`. This attempt has another error: `mySet.add(p)`.

After getting `Accepted` in my second attempt, I tried **Better solution** in third and fourth attempt. Third attempt was incomplete, I think I misclicked `submit`. Fourth attempt got `Accepted`.

One thing to note in implementing **Better solution** is that in every iteration, we should not forget to test whether faster pointer is `NULL` after it moves the first step before it moves the second step.

