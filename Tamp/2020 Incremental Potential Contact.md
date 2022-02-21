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
[Variational Integrators and the Newmark Algorithm for Conservative and Dissipative Mechanical Systems](./2000%20Variational%20Integrators%20and%20the%20Newmark%20Algorithm%20for%20Conservative%20and%20Dissipative%20Mechanical%20Systems.md)

## Smoothly clamped barriers
$$b(d,\hat{d})$$

## Implementation