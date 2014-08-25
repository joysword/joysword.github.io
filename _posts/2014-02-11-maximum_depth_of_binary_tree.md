---
layout: post
date: 2014-02-11 17:00
title: "[LeetCode] Maximum Depth of Binary Tree"
category: LeetCode
tags: LeetCode algorithm interview
---

### Description
> Given a binary tree, find its maximum depth.

> The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

### Solution
[Maximum Depth of Binary Tree](http://oj.leetcode.com/problems/maximum-depth-of-binary-tree/) is not difficult in term of algorihtm but there are some things we need to care about during writing the code.

<!--more-->

I used recursion to solve this problem. The basic idea is that the depth of a tree is either the depth of left sub-tree plus one or the depth of right sub-tree plus one, whichever is larger.

The only thing we need to be careful about is NULL pointer. If the the node is an empty node (or the pointer to the node is a NULL pointer), we return 0, otherwise we recursively get the depth of left and right children and return the larger one plus one.

### Summary
So for this problem, the algorithm is to use recursion to count from leaf to root.

The depth of a tree is the depth of the larger one among the depth of left sub-tree and right sub-tree.

### More
My first attempt got an `Compile Error` because I used `.` when I should use `->`.

After fixing this mistake, I got a `Runtime Error`. My thought was this was because I tried to use a member of a structure, but actually the pointer to that structure is a NULL pointer.

{% highlight c++ linenos %}
int maxDepth(TreeNode *root) {
	if (root->left == NULL && root->right == NULL) {
		return 1;
	}
	else {
		int a = maxDepth(root->left)+1;
		int b = maxDepth(root->right)+1;
		return (a>b)?a:b;
	}
}
{% endhighlight %}

In `line 2` I only test leaf nodes (both left and right children are NULL). But there are also nodes that have only one child. In this case, one of the `line 7` and `line 8` will fail because in the next level of calling of `maxDepth()`, `root->left` or `root->right` will not be valid.

Following is the code from my third attempt:

{% highlight c++ linenos %}
int maxDepth(TreeNode *root) {
	if (root->left == NULL && root->right == NULL) {
		return 1;
	}
	else {
		if (root->left == NULL) {
			return maxDepth(root->right)+1;
		}
		else if (root->right == NULL) {
			return maxDepth(root->left)+1;
		}
		else {
			int a = maxDepth(root->left)+1;
			int b = maxDepth(root->right)+1;
			return (a>b)?a:b;
		}
	}
}
{% endhighlight %}

I made the code more complicated. This would probably pass most of the test cases but one. When the tree is itself an empty tree, the code will fail.

That was when I figured out I could actually keep the code very simple and straight forward.

{% highlight c++ linenos %}
int maxDepth(TreeNode *root) {
	if (root == NULL) {
		return 0;
	}
	else {
		int a = maxDepth(root->left)+1;
		int b = maxDepth(root->right)+1;
		return (a>b)?a:b;
	}
}
{% endhighlight %}
