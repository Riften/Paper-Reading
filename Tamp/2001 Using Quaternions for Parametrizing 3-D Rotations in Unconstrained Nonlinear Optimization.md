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
在 $h_0$ 的超平面可以定义为

$\{x| h_0^Tx = 1\}$