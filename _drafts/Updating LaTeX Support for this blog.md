---
layout: post
title: Updating LaTeX Support for this blog
date: 2015-04-29 18:00
category: Techy
tags: LaTeX Jekyll MathJax
---

Since I wrote {% for post in site.posts %}{% if post.title contains 'LaTeX Support for Jekyll Blog
' %}[LaTeX Support for Jekyll Blog
]({{ post.url }}){% endif %}{% endfor %}, more than one year has passed.

As I've been searching for a wonderful app to write Markdown during this period of time, one of the ideal solution that I hoped for is to have the same rendering for the app and for my Jekell blog.

Today I just found the perfect app, [Macdown](http://macdown.uranusjr.com/), to be used for writing my blog articles.

Only thing I found in MacDown that is not perfect is when I include LaTeX in my article, it becomes a little laggy. Other features are all perfect.

I will not discuss Macdown in detail in this post, maybe later in another post. In this post, I will focus on the updated LaTeX setting of this blog.

One reason for why we need to add customized setting is 

You just need to use `redcarpet` as your markdown renderer, and add MathJax script to your main layout file.

The reason I'm not using the extra magic script is that in this way I can have the same . The only thing I need to keep in mind is to escape backslashes in my markdown manuscript.
