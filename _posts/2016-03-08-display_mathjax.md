---
published: true
title: Dispaly Math equations and symbols
category: Latex
tags: 
  - latex
layout: post
---

\\[ \mathbf{X} = \mathbf{Z} \mathbf{P^\mathsf{T}} \\]

$$ \mathbf{X} \_{n,p} = \mathbf{A} \_{n,k} \mathbf{B} \_{k,p} $$

$$ \mathsf{Data = PCs} \times \mathsf{Loadings} $$

$$a^2 + b^2 = c^2$$

The question is that:

We have two matrices $A,B\in \mathbf{C}^{n\times n}$, A is nonsingular and B is singular, let $||\cdot ||$ be $\textbf{any}$ matrix norm, prove

$||A-B||\geq 1/||A^{-1}||$.

My idea is 

$||(A-B)||\cdot||A^{-1}||\geq||A^{-1}(A-B)||=||I-A^{-1}B||$

And I am confused here. How to calculate the right side?

$\color{red}{Note:}$ the original question may be wrong when the norm doesn't have sub-multiplicative property. See the comments below.

HINT:

$B= A+ (B-A) = A\cdot( I - A^{-1}(A-B) ) = A \cdot (I - C)$

where $C = A^{-1}(A-B)$.  Assume that $||A-B||< ||A^{-1}||^{-1}$. Then $||C||\le ||A^{-1} || \cdot ||A-B|| < 1$. Now use the following fact:

If $C$ is a matrix with norm $||C||<1$ then the series $\sum_{n\ge 0} C^n$ is absolutely convergent and its sum is the inverse of $I-C$. In particular: $||C||<1$ implies $(I-C)$ invertible. 

Use the above to conclude $B$ is invertible, contradiction.

