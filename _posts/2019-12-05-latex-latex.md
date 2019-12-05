---
title: 'Latex latex'
author: "Nur Andi Setiabudi"
date: "December 5, 2019"
layout: post
type: post
published: yes
latex: true
tags:
- latex
excerpt: How to enable Latex support in Markdown for your Jekyll blog without the use of external Jekyll plugins. This will allow you to benefit from beautiful typesetting even when using Github Pages.
---
I will illustrate how to enable Latex support in Markdown for your Jekyll blog without the use of external Jekyll plugins. This will allow you to benefit from beautiful typesetting even when using Github Pages, especially if you’re mathematically inclined like me and wish to better disperse your writings in a clearer fashion. I only assume you’re familiar with Jekyll and are proficient working with it, in order to save time reading about the nitty gritties of Jekyll.

```
Here, have some $$\pi$$.

The greatest equation known to man is: 

$$e^{ix} = \cos{x} + i\sin{x}$$
```

Will be rendered to:

Here, have some $$\pi$$.

The greatest equation known to man is: 

$$e^{ix} = \cos{x} + i\sin{x}$$


And

$$c = \pm\sqrt{a^2 + b^2}$$

And

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$


That is it!

