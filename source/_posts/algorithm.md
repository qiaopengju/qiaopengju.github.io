---
title: 算法导论课
hide: false
math: true
mermaid: false
comment: true
date: 2021-09-09 08:53:16
categories:
tags:
index_img:
banner_img:
excerpt: algorithm course
---

### Asymptotic notations

* O notion
  *  f(n) $\in$ O(g(n)), $\exists n_0 > 0, c > 0, n >= n_0 \Rightarrow f(n) \leqslant cg(n)$
* o notion
* $\Theta$ notion
* $\Omega$ notion
* $\omega$ notion

###Common functions

* Floors and Ceilings
* Fibonacci Number
  * $ F_i = F_{i-1} + F_{i-2}$
  * $F_i = \frac{\frac{(1+\sqrt{5}}{2})^i - \frac{(1-\sqrt{5}}{2})^i}{\sqrt{5}}$
  * $F_i \in \Theta(2^n)$

### Master Theorem

$$T(1) = \Theta(1), T(n) = \sum_{i = 1}^{a}T(n_i) + f(n)$$

