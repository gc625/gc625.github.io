---
title: "Math Seminar"
enableToc: true
---

--

$\newcommand{\R}{\mathbb{R}}$
$\newcommand{\calH}{\mathcal{H}}$
$\newcommand{\la}{\langle}$
$\newcommand{\ra}{\rangle}$
--
# Hilbert Spaces
- A [Vector Space](<notes/definitions#Vector Spaces>) equipped with an [inner product](<notes/definitions#Inner Product>) which defines a [distance function](<notes/definitions#Metric>) for which it is a [complete metric space](<notes/definitions#Complete Metric Space>)
- In a Hilbert Space, we use the norm $\|x\|=\sqrt{\langle x, x\rangle}$
- and the metric is defined as: $$d(x,y)=\|x-y\|=\sqrt{\langle x-y,x-y\rangle}$$

# Repoducing Kernel Hilbert Spaces
For a set $X$, let $\mathbb{R}^X$ denote the set of functions $X \mapsto \mathbb{R}$ . We endown $\mathbb{R}^X$ with the foowing operations: $$(f_1+f_2)(x)=f_1(x)+f_2(x)\quad,\quad(a\cdot f)(x)=af(x)$$
for it to become a vector space. 

## Evaluation functionals
In this case, linear functionals, as defined as members of the [dual space](<definitions#Dual Spaces>)  of $\mathbb{R}^X$, are linear functions of the form $$\varphi:\mathbb{R}^X\to\mathbb{R}$$A specfial linear functional $e_x$ called the **evaluation functional**, sends a function $f$ to its value at a point $x$:$$e_x(f)=f(x)$$
When this special functional is bounded, the set $\mathbb{R}^X$ takes on a lot of structure.

## RKHS Definition
Let $X$ be a nonempty set. $\mathcal{H}$ is a Reproducing Kernel Hilbert Space on $X$ if:

1. $\calH$  is a vector subspace of $\R^X$
2. $\calH$ is equipped with inner product $\la \cdot,\cdot\ra$ 
	- so $\calH$ is a Hilbert Space
3. For all $x\in X$, the linear evaluation functional $e_x:\calH\to \R$ is bounded.


# Riesz Representation Theorem 
If $\phi$ is a bounded [linear functional](<definitions#Linear Functional>) on a hilbert space $\calH$, then there is a unique $g\in\calH$ such that $$\phi(f)=\la g,f\ra$$for all $f\in\calH$

## Collary 1
Let $\calH$ be a RKHS on $X$. For every $x\in X$, there exists a unique $k_x\in \calH$ such that $$\la k_x,f\ra=f(x)$$for all $f\in\calH$

### Note
- $k_x$ and $f$ are functions that sends $X\to\R$.  so $\la k_x,f\ra$ evaluates to $\R$. 
- this collary is just saying that the Riesz Representation Theorem applies to RKHS


# Reproducing Kernel
The function $K: X\times X \to\R$ defined by $$K(x,y)=k_y(x)$$
is called the reproducing kernel of $\calH$.

# (Theorem) Equivalence Between Kernels and RKHS
Every RKHS has a unique reproducing kernel, and every reproducing kernel induces a unique RKHS.

- RKHS induces Reproducing Kernel
	- follows by Riesz Representation Theorem 
- Reproducing Kernel induces RKHS 
	- follows by Cauchy-Schwartz: If $K$ is a reproducing jernel on Hilbert space $\calH$, then $$e_x(f)=\la k_x,f\ra\leq\|k_x\|\|f\|=\sqrt{K(x,x)}\cdot \|f\|$$
	- so $e_x$ is bounded, ans $\calH$ is an RKHS



# Examples of RKHS 

## Linear Functions in $\R^d$
- Let $\calH= \R^d$ with canonical basis vectors $e_1,\ldots,e_d$, and the standard inner product $$\la x,w\ra=\sum^n_{i=1}x_iw_i$$
- $X$ is the discrete set $\{1,\ldots,d\}$ 
- $e_i\in\calH$ are the kernel functions, since $$\la e_i,x\ra= x(i)=x_i$$
- 