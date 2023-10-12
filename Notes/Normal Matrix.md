# Problem Statement

^a45ad7

When a transformation is applied on the object:
$T$ is the original vertex object, and $T'$ is that after a transformation $f(\cdot)$ . So as to the normal vector $N$.
![[Pasted image 20231012211851.png|200]] ![[Pasted image 20231012211903.png|200]]
As is shown, the $T'$ and $N'$ is not perpendicular to each other after the transformation, causing the problem.
## How to Solve the Problem
First: DO NOT USE THE  HOMOGENEOUS COORDINATES.
```c
N' = vec3(TransformationMatrix * vec4(N, 0.0));
```
Since position is meaningless on normal vertex: it is just a DIRECTION.

Second: to fix the distortion problem
$N'$ should be perpendicular to $T'$, instead of deriving from $f(N)$ .
We write the $f(\cdot)$ in the form of matrix multiplication:
$$N' = Trans * N $$
Let's have a look on $T$:
$T$ is the subtraction of 2 vertex vector: $$T = P_1-P_2$$
and $T'$ and be represented as below: $$T' = Trans * T = Trans * (P_1-P_2)$$
thus: $$
\begin{align}
T' =& Trans *P_1 - Trans*P_2\\
T' =& P_1' - P_2'
\end{align}
$$
We do the same thing on $N$:
$$ N = Q_1 - Q_2$$
Sadly, $Trans$ matrix is not appropriate to apply on $N$, since it will cause the [[#^a45ad7|Problem]]. 