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

> 为什么这里直接用了 $f_e$ 而不是用求出来的上一步的 $\ddot{q}_k$？实际实现里面这个 $f_e$ 是怎么求的？理论上这应当是 环境中的保守力 + barrier 求导得到的 contact force + 摩擦力。

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
这里的 Barrier Function 和内点法中的含义相同，将 Constrainted Optimization 问题转化成 Unconstrained Optimization 问题，要求是
- 连续
- 在 primitive 之间相距足够远的时候值为 0，这样即使通过预先计算去掉相距远的 barrier function 也不会影响最终结果
- 在 primitive 之间距离趋近于 0 的时候趋近于无穷，这也是 Barrier Function 作为 “屏障” 的意义，本文的 Barrier 条件可以保证永远不会穿模。

定义 $\hat{h}$ 为两个 primitive 之间发生 contact 的最大距离，定义 Barrier Function 为

$$\begin{aligned}b(d,\hat{d})=\left\{\begin{array}{lr}
-(d-\hat{d})^2\ln(\frac{d}{\hat{d}}), & 0 < d < \hat{d}\\
0, &d\geq \hat{d}
\end{array}\right.\end{aligned}$$

加上 $(d-\hat{d})^2$ 的目的是让 barrier 在 $h=\hat{h}$ 处二阶连续，从而可以用 Newton-type methods 求解极值。

定义 $\mathcal{C}$ 为所有不相邻的 点-面 / 边-边 primitive pair，原本最小化 IP 变成了这里的 Barrier-Augmented Incremental Potential

$$B_{t}(x) = E(x, x^t, v^t) + \kappa\sum_{k\in\mathcal{C}}b(d_k(x))$$

上式中，如果将整体看作一个 Incremental Potential，Barrier Function 在这里也充当了一个势能项，该项在两个 primitive 接触的时候为无穷大。

## Implicit Euler
最小化 $B_t(x)$ 即求解 $\nabla_x B_t(x, \hat{d})=0$，即

$$M\frac{x-\hat{x}}{h^2} = -\nabla\Psi(x) + \sum_{k\in\mathcal{C}} \lambda_k \nabla d_k(x)$$

上式形式即为一个 Newton-Euler 方程。这里的 $\lambda$ 就是等效的 contact force

$$\lambda_k = \frac{\kappa}{h^2}\frac{\partial b}{\partial d_k}$$

## Intersection-aware Line & CCD
- [Note: Optimization (Newton Method)](./Marc%20Toussaint/Optimization%2001.md)

基本的想法是：先用 Continuous Collision Detection 找到一个梯度方向上的最大步长，然后在做 back-tracking line search。

## neo-Hookean material
### FEM & MPM
- [Wikipedia: Finite Element Method](https://en.wikipedia.org/wiki/Finite_element_method)

Finite Element Method (FEM) 有限单元法。

## Intersection-aware line search


## Implementation