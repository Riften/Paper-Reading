# Using Quaternions for Parametrizing 3-D Rotations in Unconstrained Nonlinear Optimization
由于四元数的 normalization 限制，四元数的四个参数自由度只有3。为了将四元数直接用在 Unconstrained Nonlinear Optimization 里面，需要找到一个用三个参数表示四元数更新的方法。

最 Naive 的方法：

$$h = \left( \sqrt{1-\frac{(\kappa_x^2 + \kappa_y^2 + \kappa_z^2)}{4}}, \frac{\kappa_x}{2}, \frac{\kappa_y}{2}, \frac{\kappa_z}{2}\right)$$

但是这个参数化方法同样需要保证旋转角为一定范围内（似乎是$-\pi$ 到 $\pi$ ?)，即也有 constraint。

## 基本思路
设原本四元数为 $h_0$，优化过程中更新为 $h_Z$。

由于四元数的自由度为 3，所以中间的变化量可以用一个 3 维向量表示。本文的想法是，$h_0$ 和 $h_z$ 都在四维空间中的单位球上，他们之间存在一个劣弧（小于半圆的弧）连接。这个劣弧的大小和方向就可以表示四元数的更新。

为了参数化这个劣弧，取和单位球在 $h_0$ 处相切的超平面，劣弧在该平面上的投影 $v_4$ 可以作为该劣弧的一种参数化形式。而 $v_4$ 由于在超平面上，所以可以取与 $h_0$ 垂直的三组正交基，以此为基的 local frame 中 $v_4$ 可以表示成一个三位向量 $v$，由此完成了自由度为 3 的参数化。

接下来要做的是
- 给定 $h_0$，求与单位球相切于 $h_0$ 的平面。
- 给定该平面上的一个3维向量 $v$，求出其在 4 维空间中对应的向量 $v_4$
- 根据 $h_0$ 和 $v_4$ 求 $h_Z$

## 方法
### 求超平面的正交基
在 $h_0$ 处与单位球超平面可以定义为

$$\{x| h_0^Tx = 1\}$$

$h_0$ 此时就是该超平面的一个 normal vector，设 $h_0 = (n_1, n_2, n_3, n_4)^T$，注意在优化过程中 $h_0$ 是定值。则对超平面上任意一点 $x = (x_1, x_2, x_3, x_4)$ 有 $n_1 x_1 + n_2 x_2 + n_3 x_3 + n_4 x_4 = 1$。

不失一般性，设 $x_1 \neq 0$，则有

$$\begin{aligned}
    x_1 = \frac{1}{n}(1-n_2x_2 - n_3x_3 - n_4x_4)\\
    x2 = \lambda, x_3 = \mu, x_4 = \upsilon
\end{aligned}$$

此时超平面上任意一点的坐标可以表示为

$$\begin{aligned}
    x &= a + B'(\lambda ~~\mu~~ \upsilon)^T\\
    a &=(\frac{1}{n_1}~~0~~0~~0)^T\\
    B' &= \left[\begin{array}{c}
        -\frac{n_2}{n_1}& -\frac{n_3}{n_1} & -\frac{n_4}{n_1}\\
         1 & 0 & 0\\
          0 & 1 & 0\\
          0 & 0 & 1
    \end{array}\right]
\end{aligned}$$

换言之，我们找到了三个基向量（$B'$的三列），可以将任意三维变量映射到超平面中。但是这三个基互相不垂直，我们希望找到一组单位正交基。方法是用 SVD 分解

$$B' = B\Sigma V^T$$

此 SVD 分解只有三个奇异值，所以有

$$B_{4\times 4}\Sigma_{4\times 3}V^T_{3\times 3} =B_{4\times 3}\Sigma_{3\times 3}V^T_{3\times 3}$$

这里的 $B$ 矩阵和 $B'$ 矩阵张成的空间是一致的，所以用 $4\times 3$ 矩阵 B 的列向量作为一组单位正交基，将任意三维向量映射到超平面上。

到这里我们有了从任意三维向量 $v$ 到超平面上坐标 $v_4$ 的映射 $v_4 = Bv$

## 计算 $h_Z$
等价于：已知单位向量 $h_0$，与 $h_0$ 垂直的超平面上的坐标 $v_4$，求 $h_0, v_4$ 张成的3维超平面与4维单位球的交点 $h_Z$。

公式如下

$$h_Z = \sin(|v_4|) \frac{v_4}{|v_4|} + \cos(|v_4|)h_0$$