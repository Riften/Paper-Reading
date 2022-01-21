# Physics Violation Cost
基于 Optimization 的方法一大优点就是允许优化过程中的 Trajectory 不遵守物理限制。物理上的限制通常以一个 cost 的形式给出，通过最小化该 cost，达到逐渐遵守物理限制的效果。

## CIO 中的 Physics Violation Cost
给出 trajectory $q$，首先计算达到 $q$ 需要的和外力

$$\tau=M\ddot{q} + C\dot{q}+g$$

- $M\ddot{q}$： 惯性矩阵乘加速度
- $C\dot{q}$： 偏向力的负值
- $g$: 重力等恒定力的负值

$\tau$ 是由 Kinematic 决定的力，相当于 target。

然后求解子优化问题
$$\begin{aligned}
    f,u = \argmin_{\tilde{f}, \tilde{u}}&\lVert J^T\tilde{f} + B\tilde{u} - \tau \rVert^2 + \tilde{f}^TW\tilde{f} + \tilde{u}^TR\tilde{u}\\
    \text{subject to  }& A\tilde{f} \leq b
\end{aligned}$$
- $J^T\tilde{f}$: contact force
- $B\tilde{u}$: applied force
- $A\tilde{f} \leq b$: 摩擦锥限制

这样可以得到能达到 $\tau$ 的最接近的 $f,u$，然后将 $cost = \lVert J^Tf+Bu-\tau\rVert^2$ 作为 Physics Violation Cost

在 CIO 的求解过程中，子优化问题实际上是一个 Inverse Dynamics 过程，只不过使用的 物理模型非常简单。

总体过程可以总结为
- 根据 $q$ 求当前需要的力 $\tau = f(\ddot{q}, \dot{q}, q)$
- Inverse Dynamics 求出可以施加的力 $u$
- $cost = \lVert u - \tau\rVert^2$

## Brax