---
title: "Definitions"
enableToc: true
---



# Dual Spaces
Given vector space $V$ over field $F$, its dual space $V^*$ is the set of all [linear transformation](<definitions/Linear Transformations>) $$\varphi: V\to F$$Note that $\varphi$ is called a linear functional (or linear form, one-form) 

In other words, $V^*$ is just the set of all functions that take in a vector $v\in V$, and spits out an element in $F$ 


# Linear Functional
A linear functional, linear form, or one-form on a vector space $V$ over $\mathbb{R}$ is a linear transformation $$\varphi:V\to\mathbb{R}$$
it is bounded if there exists an $M>0$ such that $$\|\varphi(x)\|\leq M\|x\|$$ for all $x\in V$

# Linear Transformations
Given two vector spaces $V$ and $W$ over field $F$. A mapping $$f:V\to W$$ is called linear (or $F$-linear, homomorphism of $F$-vector spaces) if 
1.  $f(v_1+v_2)=f(v_1)+f(v_2)$
2.  $f(\lambda v_1)=\lambda f(v_1)$

## Variations
- **Isomorphism**: Bijective linear transformations
- **Endomorphism**: Linear map from vector space to itself
- **Automorphism**: Bijective endomorphism



# Vector Spaces
- A set $V$ that is closed under finite vector addition and scalar multiplication

# Inner Product
A mapping on a vector space $V$ over a field $F$: $$\langle\cdot,\cdot\rangle: V\times V \to F$$
with 
1. conjugate symmetry: $\langle x,y\rangle = \overline{\langle x,y\rangle}$
2. linearity in the first argument: $\langle ax+by,z\rangle = a\langle x,z\rangle+b\langle y,z\rangle$
3. Positive-definiteness: $\langle x,x\rangle \geq 0$

# Metric
or distance function, is a function $$d: X\times X \to \mathbb{R}$$
such that for all $x,y,z\in X$, the metric has the following properties
1. Identity of indiscernibles: $d(x,y)=0 \iff x=y$ 
2. Symmetry: $d(x,y)=d(y,x)$
3. Triangle Inequality:$d(x,z)\leq d(x,y)+d(y,z)$

# Complete Metric Space
A metric space $M$ where every [Cauchy sequence](<definitions#Cauchy Sequence>) of points in $M$ converges to a limit in $M$.


# Cauchy Sequence
In a metric space $(X,d)$, a sequence $$x_1,x_2,x_3,\ldots$$where for all real number $\varepsilon>0$ there is a positive integer $N$ such that for all $m,n>N$:
$$d(x_m,x_n)<\varepsilon$$



# Groups
A group is an algebraic structure of one set plus one operator. We denote the set as $A$ and the operation as $\cdot$, then the group can be denoted as $G=(A,\cdot)$. We say $G$ is a \textit{group} if the operation satisfies the following conditions:

- Closure:   $\forall a_1, a_2 \in A, \ a_1 \cdot a_2 \in A$
- Combination: $\forall a_1, a_2, a_3 \in A, \ (a_1 \cdot a_2) \cdot a_3 = a_1 \cdot ( a_2 \cdot a_3)$
- Unit element: $\exists a_0 \in A, \ \mathrm{s.t.} \ \forall a \in A, \ a_0 \cdot a = a \cdot a_0 = a$.
- Inverse element: $\forall a \in A, \ \exists a^{-1} \in A, \ st \ a \cdot a^{-1} = a_0$.


# Lie Group
A group with continuous elements 