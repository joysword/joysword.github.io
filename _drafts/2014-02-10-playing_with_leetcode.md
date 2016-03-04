---
layout: post
title: "[LeetCode] Playing with LeetCode"
category: LeetCode
tags: LeetCode
---

This semester I started cracking [leetcode.com](http://oj.leetcode.com/), in order to prepare for future interviews and to keep my hands dirty during the interim   of projects.

I plan to write down solutions and maybe some thoughts and mistakes for every problem I got `Accepted`.

Here is a list of all my posts (updating):

<!--more-->

{% for post in site.categories.leetcode reversed %}
{% if forloop.first == false %}
* {{ post.date | date: "%Y-%m-%d" }}&nbsp;&nbsp;<a href="{{ post.url }}">{{ post.title }}</a>
{% endif %}
{% endfor %}


