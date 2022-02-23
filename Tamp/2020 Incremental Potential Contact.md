# Incremental Potential Contact: Intersection- and inversion-free Large-Deformation Dynamics

## Extended-Value Action Functional
拉格朗日力学中，定义 action 泛函为

$$\mathcal{S} = \int_{t_1}^{t_2}L(q(t), \dot{q}(t), t)dt$$

这里的被积分泛函 $L(q(t), \dot{q}(t), t)$ 称为 Lagrangian。action 可以看作是一个关于函数 $q(t)$ 的泛函，$q(t)$ 同时也是 trajectory，求解系统的运动轨迹即求解泛函取极值的解 $q(t)$。在没有外力的情况下，action 极值等价于 Stationary-action principle：

$$\delta \mathcal{S} = 0$$

即能量变化值的任意微分为 0。

IPC 扩展了 action 泛函的形式，使用以下 extended-value action:

$$S(x) = \int_0^T\Large(\normalsize \frac{1}{2}\dot{x}^TM\dot{x} - \Psi(x) + x^T(f_e + f_d)\Large)\normalsize dt$$
- $x$: Mesh 顶点
- $M$： 惯性矩
- $\Psi(x)$: Hyper-elastic deformation energy 弹性形变能量，可以看作是势能的一部分
- $f_e$: 外力
- $f_d$: dissipative frictionl forces，摩擦力，会消耗能量

IPC直接计算可弹性形变的 mesh model，model 的每个顶点的位置函数 $x(t)$ 是定义域。

## Incremental Potential
[Variational Integrators and the Newmark Algorithm for Conservative and Dissipative Mechanical Systems](./2000%20Variational%20Integrators%20and%20the%20Newmark%20Algorithm%20for%20Conservative%20and%20Dissipative%20Mechanical%20Systems.md) 中从 Newmark Method 出发，给出了一个可行的 Integrator

$$q_{k+1} = \argmin_{q_{k+1}}\frac{1}{2 h^2}(q_{k+1}-q^{pre}_{k+1})^TM(q_{k+1}-q^{pre}_{k+1}) +\beta V_k(q_{k+1}) -\beta f_{k+1}^{ext}q_{k+1}$$

本文中也采用一样的 Euler-Lagrange Equation。
- $q^{pre}_{k+1} = q_k + h\dot{q}_{k} + \frac{h^2}{2}[(1-2\beta)\ddot{q}_{k}]$，类似的定义 $\hat{x} = x^t + hv^t + h^2M^{-1}f_e$
- $V_k$ 为势能+耗散能，等价于这里的 $\Psi(x) - x^Tf_d$

$$\begin{aligned}
    &E(x, x^t, v^t) = \frac{1}{2}(x-\hat{x})^TM(x-\hat{x}) - h^2x^Tf_d + h^2\Psi(x)\\
    &x^{t+1} = \argmin_x E(x,x^t, v^t), ~~~~x^\tau\in \mathcal{A}
\end{aligned}$$

$x^\tau\in \mathcal{A}$ 指的是 $x^t$ 到 $x^{t+1}$ 之间的 trajectory 再 admissible trajectories 内。

要求解上面的式子，本文需要做到
- 对 Incremental Potential 进行近似。
- 正确处理摩擦带来的耗散
- 找到合适的方法处理 $x^{\tau}\in\mathcal{A}$ 这个 constraint
  - 通常的 constrained optimization 里面，通过再原本的目标函数中加上惩罚项，转化为 unconstrained optimization 问题求解，这里也一样，通过应以一个 barrier term，使其满足 1）两个表面离得远的时候为 0 2）离得太近的时候发散。

## Smoothly clamped barriers
$$b(d,\hat{d})$$

## Implementation