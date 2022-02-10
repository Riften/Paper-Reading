# Shooting & Collocation
[Northwestern: Modern Robotics 10.7 Nonlinear Optimization](https://modernrobotics.northwestern.edu/nu-gm-book-resource/10-7-nonlinear-optimization/#department)

Motion Plan 问题的 Non-Linear Optimization 形式

$$\begin{aligned}
    \text{find}& & &u(t), q(t), T\\
    \text{minimizing}& & &J(u(t), q(t), T)\\
    \text{subject to}& & &\dot{x} = f(x(t), u(t))\\
    & & &u\in \mathcal{U}\\
    & & &q(t)\in \mathcal{C}_{free}\\
    & & &x(0)\in x_{start}\\
    & & &x(T)\in x_{goal}
\end{aligned}$$

- $u$ control
- $q$ trajectory
- $T$ Dutation
- $J$ Cost function
- $\dot{x} = f(x(t), u(t))$ 为 dynamic equation

## Shooting Method
通过改变 $u$ 来优化整个过程，通过 simulate 来满足 system dynamic。

$u$ 必须是经过离散化的，比如 linear 或者 spline 连接的点。

- 首先给出 initial guess $u_k$, $k=0$
- 通过对离散 $u$ 进行插值和运动方程，得到 trajectory $q_k$。这实际上是 simulation 过程。$q_k$ 可能不满足除了 system dynamic 以外的所有其他 constraint
- 计算梯度 $\frac{\partial\text{constraints and cost}}{\partial\text{trajectory}}\Rightarrow \frac{\partial\text{trajectory}}{\partial\text{controls}}$
- 根据梯度更新$u_{k+1} = \alpha\delta u_k$


## Collocation
同时将 $u(t),q(t),T$ 作为优化变量。不通过 simulation 来保证 dynamics，而是直接将 dynamics 作为 constraints。需要通过优化 dynamics constraint 来保证 control trajectory 和 system trajectory 是一致的。

Collocation 将 dynamics 直接作为 constraints，所以在优化过程中可以不满足 dynamics 限制。而 Shooting 直接依赖于 simulation 过程来更新 control，所以无法突破 dynamics 限制。

定义 physics violation loss 也是 collocation 算法的一个核心问题。