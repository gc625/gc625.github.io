---
title: Lie Algebra and Lie Groups
enableToc: true
---

*Special Orthogonal Group*: the group of rotational matrices 
$$
\mathrm{SO}(3) = \{ \mathbf{R} \in \mathbb{R}^{3 \times 3} | \mathbf{RR}^T = \mathbf{I}, \det(\mathbf{R})=1 \}.
$$
Special Euclidean Group: the group of transformation matrices
$$
\mathrm{SE}(3) = \left\{ \mathbf{T} = \left[ {\begin{array}{*{20}{c}}
\mathbf{R} & \mathbf{t} \\
{{\mathbf{0}^T}} & 1
\end{array}} \right]
\in \mathbb{R}^{4 \times 4} | \mathbf{R} \in \mathrm{SO}(3), \mathbf{t} \in \mathbb{R}^3\right\}
$$

These are groups because they satisfy all the [required properties](<notes/definitions#Groups>), but furthermore, because the elements of the set are continuous (smooth), they actually also form what we call a Lie Group


# Lie Algebra
Let $\mathbf{R}(t)$ be the rotation of a camera that changes continuously over time, we know that it satisfies:
$$
\begin{equation}
\mathbf{R}(t)\mathbf{R}(t)^T = \mathbf{I}
\end{equation}
$$
Take derivative yields (by chain rule):
$$
\dot{\mathbf{R}}(t)\mathbf{R}(t)^T+\mathbf{R}(t)\dot{\mathbf{R}}(t)^T=0
$$
which equals

