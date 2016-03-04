---
layout: post
title: "[LeetCode] Best Time to Buy and Sell Stock II"
date: 2014-02-12 13:00
category: LeetCode
tags: LeetCode algorithm interview
---

### Description
> Say you have an array for which the ith element is the price of a given stock on day i.

> Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times). However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

### Solution
[Best Time to Buy and Sell Stock II](http://oj.leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/) is easy to solve. Assume we buy stock in day `i`, and sell it in day `j`, where `j>i`, then if `prices[j]>prices[i]`, we can make profit. However, if in some day between day `i` and day `j`, the price is lower than `prices[i]`, if we buy stock in that day rather than in day `i`, we can make more profit.

<!--more-->

Therefore, our solution is to test if price today is higher than price yesterday. If so, the difference in price is our maximum profit for today. Go over all days and add them up will give us the final result.

### Summary
The answer is the sum of all differences between price in a day and price in the day before that day such that the differnces are posivite.

### More
My first attempt got `Wrong Answer` because it was with a stupid mistake. I put `return` statment inside `for` loop.

My second attempt still got `Wrong Answer`, because I have another mistake in my code. I calculated the bool value of whether current index is larger than previous one instead of the actual valud of the differnce between them.