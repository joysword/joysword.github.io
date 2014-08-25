---
layout: post
date: 2014-02-11 20:00
title: "[LeetCode] Reverse Integer"
category: LeetCode
tags: LeetCode algorithm interview
---

### Description
> Reverse digits of an integer.

> **Example1:** x = 123, return 321

> **Example2:** x = -123, return -321

### Solution

According to the two examples in the description of [Reverse Integer](http://oj.leetcode.com/problems/reverse-integer/), we can easily see that if we don't consider negative sign, `x=123` and `x=-123` can be solved in the same way.

Thus I decided to first get rid of the negative sign if necessary, then reverse the positive number, then add the negative sign back if necessary.

<!--more-->

In order to reverse the positive number, we traverse all the digits from right to left, everytime multiply previous result by 10 and add the curren digit.

So the pseudo code is like following:

{% highlight c++ %}
int reverse(int x) {
    if (x is negative) y = -x, otherwise y = x;
    int res = 0;
    while (y>0) {
        multiply res by 10;
        add res by y's right most digit;
        get rid of y's right most digit;
    }
    return y or -y;
}

{% endhighlight %}

### Summary
In each iteration, multiply previous result (initial value 0) by 10, then take the right most digit before get rid of it. Negative sign is dealt separately.
