---
title: Lambert Azimuthal Equal-Area Projection
date: 2021-06-18 10:30:00
---

I was reading an article on Lambert Azimuthal Equal-Area Projection on Wikipedia.
As I did not understand the equations immediately, I had to sit down and think about them.

Let $$S^2$$ be the unit sphere in $$\mathbb{R}^3$$, and let $$S = (0, 0, -1)$$.
Pick a point $$P \in S^2 \setminus \{(0, 0, 1)\}$$.
Also, write the components of $$P$$ with subscripts, so that $$P = (P_x, P_y, P_z)$$.
We want to project $$P$$ onto $$P'$$ in the plane $$z = -1$$ using the above mentioned
Equal-Area projection.

I have tried to illustrate this situation in the following picture.
The dashed circle lies in a plane that contains the points $$O, S, P$$.
We are looking at the unit sphere "from the side", directly at this plane.
The smaller, solid circle is the unit sphere, and the line under it is the plane $$z = -1$$.

![](/assets/equal-area.png)

The distance from the origin ($$O$$ in the picture) to $$P$$, when both are projected onto
the same z-plane, is $$a = \sqrt{P_x^2 + P_y^2} = \sqrt{1 - P_z^2}$$.
This distance is fittingly represented by $$a$$ above.

The distance from $$S$$ to $$P$$, i.e. the radius of the larger dashed circle in
the picture, is

$$
\begin{align*}
r &= \Vert P-S \Vert \\
  &= \sqrt{P_x^2 + P_y^2 + (P_z + 1)^2} \\
  &= \sqrt{1 - P_z^2 + (P_z + 1)^2} \\
  &= \sqrt{2 (1 + P_z)}
\end{align*}
$$

We want to derive expressions for $$P' = (X, Y)$$. For that reason, we denote by $$k$$
the proportion between $$a$$ and $$r$$. Then we can multiply $$k$$ by the distance in the 
$$x$$ direction and by the distance in the $$y$$ direction, respectively, to get the point
$$P'$$ in our plane.

$$
\begin{align*}
k &= \frac{\sqrt{2(1 + P_z)}}{\sqrt{1 - P_z^2}} \\
  &= \frac{\sqrt{2(1 + P_z)}}{\sqrt{(1 + P_z)(1 - P_z)}} \\
  &= \sqrt{\frac{2}{1 - P_z}}
\end{align*}
$$

Finally, $$P'$$ is obtained by

$$
\begin{align*}
X &= \sqrt{\frac{2}{1 - P_z}} P_x \\
Y &= \sqrt{\frac{2}{1 - P_z}} P_y
\end{align*}
$$
