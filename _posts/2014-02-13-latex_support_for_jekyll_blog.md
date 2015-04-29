---
layout: post
title: LaTeX Support for Jekyll Blog
date: 2014-02-13 9:00
category: Techy
tags: LaTeX Jekyll MathJax
---

When I was writing {% for post in site.posts %}{% if post.title contains'Unique Binary Search Trees' %}[[LeetCode] Unique Binary Search Trees]({{ post.url }}){% endif %}{% endfor %}, I decided to use `\(\LaTeX\)` to write some formulae.

test \\(\LaTeX\\)

After googling `jekyll latex support`, I found this [article](http://liquidinertia.com/2013/07/15/latex-for-jekyll/). It took me to another [blog post](http://cwoebker.com/posts/latex-math-magic) by a high school student from Boston, which provided a nice tutorial that adds LaTeX support on Jekyll blogs by using some tricks to let [MathJax](http://www.mathjax.org/), an open source JavaSrcipt display engine for mathematics, work properly in Jekyll environment.

<!--more-->

Generally speaking, the issue that every Jekyll blog has is that when Markdown will interpret all tags even if some of them are actually LaTeX tags. For more information, please refer to [this blog post](http://cwoebker.com/posts/latex-math-magic).

The way to we use MathJax is to put LaTeX code in a `code block`, using `acute` symbol: `` ` ``. But MathJax won't render text between `<code>` tags by default since in most cases code between `<code>` tags are not LaTeX code.

To solve this issue, we need to add some JavaScript and some CSS (optional).

#### In Main Layout File
In your main layoug file, add the following code anywhere. I would like to put all my JavaScript code at the end of the file, only before the closing `<\html>` tag.

{% highlight HTML %}
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
<script>
MathJax.Hub.Config({
    tex2jax: {
		skipTags: ['script', 'noscript', 'style', 'textarea', 'pre']
	}
});
MathJax.Hub.Queue(function() {
	var all = MathJax.Hub.getAllJax(), i;
	for(i=0; i < all.length; i += 1) {
		all[i].SourceElement().parentNode.className += ' has-jax';
	}
});
</script>
{% endhighlight %}

Test fancy code block:

``` html
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
<script>
MathJax.Hub.Config({
    tex2jax: {
		skipTags: ['script', 'noscript', 'style', 'textarea', 'pre']
	}
});
MathJax.Hub.Queue(function() {
	var all = MathJax.Hub.getAllJax(), i;
	for(i=0; i < all.length; i += 1) {
		all[i].SourceElement().parentNode.className += ' has-jax';
	}
});
</script>
```

Line 1 is to include MathJax. MathJax.Hub.Config is to configure which tags to skip.MathJax.Hub.Queue adds a function to add string ` has-jax` to all LaTeX code blocks.

#### In Main CSS File
In your main CSS file, add the following code to set style for LaTeX code. On example would be:

{% highlight CSS %}
code.has-jax {font: inherit; font-size: 100%; background: inherit; border: inherit;}
{% endhighlight %}

### Examples

Here I have some examples taken from the MathJax [Demo Page](http://www.mathjax.org/demos/tex-samples/).

#### The Lorentz Equations

using `$$ .. $$`:

$$
\begin{aligned}
\dot{x} & = \sigma(y-x) \\\
\dot{y} & = \rho x - y - xz \\\
\dot{z} & = -\beta z + xy
\end{aligned}
$$

using `\\[ .. \\]`:

\\[
\begin{aligned}
\dot{x} & = \sigma(y-x) \\\
\dot{y} & = \rho x - y - xz \\\
\dot{z} & = -\beta z + xy
\end{aligned}
\\]

using `` `\[ .. \]` ``:

`
\[
\begin{aligned}
\dot{x} & = \sigma(y-x) \\\
\dot{y} & = \rho x - y - xz \\\
\dot{z} & = -\beta z + xy
\end{aligned}
\]
`

using `$ .. $`: $\sum$

using `\\( .. \\)`: \\(\sum\\)

using `` `\( .. \)` ``: `\(\sum\)`

#### The Cauchy-Schwarz Inequality

\\[ \left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right) \\]


#### A Cross Product Formula

\\[\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix}
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0
\end{vmatrix}  \\]


#### The probability of getting `\(k\)` heads when flipping `\(n\)` coins is
`
\[P(E)   = {n \choose k} p^k (1-p)^{ n-k} \]
`
#### An Identity of Ramanujan
`
\[ \frac{1}{\Bigl(\sqrt{\phi \sqrt{5}}-\phi\Bigr) e^{\frac25 \pi}} =
1+\frac{e^{-2\pi}} {1+\frac{e^{-4\pi}} {1+\frac{e^{-6\pi}}
{1+\frac{e^{-8\pi}} {1+\ldots} } } } \]
`

#### A Rogers-Ramanujan Identity
`
\[  1 +  \frac{q^2}{(1-q)}+\frac{q^6}{(1-q)(1-q^2)}+\cdots =
\prod_{j=0}^{\infty}\frac{1}{(1-q^{5j+2})(1-q^{5j+3})},
\quad\quad \text{for $|q|<1$}. \]
`

#### Maxwell&#8217;s Equations
`
\[  \begin{aligned}
\nabla \times \vec{\mathbf{B}} -\, \frac1c\, \frac{\partial\vec{\mathbf{E}}}{\partial t} & = \frac{4\pi}{c}\vec{\mathbf{j}} \\   \nabla \cdot \vec{\mathbf{E}} & = 4 \pi \rho \\
\nabla \times \vec{\mathbf{E}}\, +\, \frac1c\, \frac{\partial\vec{\mathbf{B}}}{\partial t} & = \vec{\mathbf{0}} \\
\nabla \cdot \vec{\mathbf{B}} & = 0 \end{aligned}
\]
`