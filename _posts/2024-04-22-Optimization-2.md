---
layout: post
title: "DSC243 Note (2): From Local Convergence to Global Convergence"
date: 2024-04-22 09:56:00-0400
description: "Notes of DSC 243: Advanced Optimization"
tags: "optimization, note"
categories:
giscus_comments: false
related_posts: false
related_publications: 
---

What is a solution?

- Problem: $min_{x\in A}f(x)$

- Solution: $\|f(x)-f(x^*)\|\le \epsilon$, with prob $1-\delta$

This is a **probabilistic** definition, because we usually use stochastic algorithm. As the algorithm runs longer, it **asymptotically** converges ($\epsilon$ and $\delta$ go to 0).

## Local Solution

Finding a global optimum is difficult (as discussed in the last lecture), we can talk about local solutions:

Imagine we are using gradient descent, it will stop when it reaches a local optimum.

First order stationary point (FOSP): $\nabla f(x)=0$

It doesn't specify the **stability** of the solution.

Second order stationary point: $\nabla^2 f(x)\succ 0$ and $\nabla f(x)=0$. (The Hessian matrix is positive definite + first order stationary.)

A more popular definition of second-order stationary point actually only requires positive semi-definite, but you cannot guarentee it's stable then.

Around FOSP, we can apply Taylor series (approximated to second order):

$$f(x)\approx f(x^*) + <\nabla f(x^*), x-x^*>+\frac{1}{2}(x-x^*)^T\nabla^2f(x^*)(x-x^*) = \phi(x)$$

The gradient is zero, so we only need to worry about the second-order. How would gradient descent behave?

$H:=\nabla^2 f(x)$, $h$ is the learning rate.

$$x_{t+1}=x_t - h\nabla \phi(x)=x_t - hH(x_t - x^*)$$

> Quick review:
> $ \frac{1}{2}x^TAx = \frac{1}{2}\sum_{i\ne j}2a_{ij}x_ix_j + \frac{1}{2}\sum_{i}a_{ii}x_i^2$. Looking at the gradient for $a_i$, it's $\sum_ja_{ij}x_j$, which is the $i$ th element of $Ax$. 


$$x_{t+1}-x^* = (I-hH)(x_t - x^*)$$

$N$ is the optimization step

$H=u\Lambda u^T, \Lambda$ is a diagnoal matrix composed of all eigen values.

$$x_T - x^* = (I-hH)^N(x_0-x^*)=u(I-h\Lambda)^Nu^T(x_0-x^*)$$

- Case 1: If one of the eigen values is negative, $I-h\lambda_i$ is larger than 1, so it would explode. This is a saddle point.
- Case 2: $\lambda_1 > ... \lambda_d> 0$.


> Quick review:
> The norm of a matrix: $$||A||_p=\max_{||x||_p<1} ||Ax||{_p}$$. 
> Geometrically speaking, one can imagine a p-norm unit ball, then apply the linear map to the ball. It would end up becoming a distorted convex shape and p-norm measures the longest "radius" of the distorted convex shape. According to the definition, we have $||Ax||\le ||A||\ ||x||$


$$
\begin{align*}
||x_{T}-x^*||_2 &= ||u(I-h\Lambda)^N u^T (x_0-x^*)|| \\ &\le  ||u(I-h\Lambda)^N u^T ||_2 \ ||(x_0-x^*)||_2
\end{align*}
$$


We need to have $h\le \frac{1}{\lambda}$, otherwise it will explode. (\*) Choose it to be $\lambda_1$, the max eigen value. So, in the last direction is the slowest direction, because $1-\frac{\lambda_d}{\lambda_1}$ is close to one. 

$$||x_T-x^*||_2 \le (I-\frac{\lambda_d}{\lambda_1})^T||x_0 - x^*||_2 \le \epsilon$$

It takes $T=\frac{\lambda_1}{\lambda_d}\log \frac{\|\|x_0 - x^*\|\|_2}{\epsilon}$ to converge. Here, 
$\frac{\lambda_1}{\lambda_d}$ is the condition number.

(\*) Note that, only the first-order information is visible to GD. We make use of the Hessian matrix only to find what's the best GD can achieve.

## Towards global solution

We need stronger regularization.

**Convexity**

- Second-order condition: $$\nabla^2f(x)\succeq 0$$

- First-order condition: Predicting function value with tangent, the actual function is always above the projection.

    $$<\nabla f(x), y-x> \le f(y) - f(x)$$

    Local information tells a lot about global information: You can ignore a direction if the gradient is larger than 0, because you know it will never decrease.

- Zero-order condition: The intersect line is always above the actual functions

    $$f(\lambda x + (1-\lambda) y) \le \lambda f(x) + (1-\lambda)f(y)$$

**Proof**

Hint: Think about the geometry. Increasing order need taking derivation, otherwise integration.
- 0 -> 1: 

    $$f(y+\lambda(x-y)) \le \lambda f(x) + (1-\lambda)f(y)$$

    Taking derivative of $\lambda$:

    > Quick review: Think of the limit when $\lambda \rightarrow 0$, and ignore smaller items in the Taylor expansion.

    $$<\nabla f(y+\lambda(x-y)), (x-y)> \le f(x) - f(y)$$
    
    Setting $\lambda =0$:
    
    $$<\nabla f(y), x-y> \le f(x) - f(y)$$

- 1 -> 2: 
    
    **(a)** Starting with the simple case where $d=1$.
    
    $$f(y) \ge f(x) + f'(x) (y-x)$$
    
    $$f(x) \ge f(y) + f'(y) (x-y)$$
    
    From these two inequation, we can get:
    
    $$f'(y)(x-y) \le f(x)-f(y) \le f'(x)(x-y)$$
    
    Assuming $x-y>0$,

    $$\frac{f'(x)-f'(y)}{x-y}\ge 0$$

    **(b)** For any dimensions, we want to make it into 1-d:
    
    $$g(\alpha) = f(x+\alpha v)$$
    
    We can show it's also a convex function by expanding it. So, applying the $1-d$ conclusion, we have:

    $$g''(\alpha)\ge 0$$

    $$v^T\nabla f(x+\alpha v)v \ge 0$$
    
    Taking $\alpha=0$, we get the definition of semi-positive matrix.

**Strongly convex**

> Intuition: $x^2$ is the standard convex function. If a function is "more convex" than $mx^2$...

Definition of $m$-strong convex:
$$ f(x) - \frac{m}{2} ||x||^2 \text{ is convex.}$$

- Second-order: $$\nabla^2f(x)\succeq m\cdot I$$ $$\lambda_{\min}(\nabla^2f(x))\ge m$$
- First-order: $$f(y)-f(x)\ge <\nabla f(x), y-x> + \frac{m}{2} \|\|y-x\|\|^2$$
- Zero-order: Skipped. (simply apply the definition)

In the next class, we will show how to use these conditions to derive global convergence.