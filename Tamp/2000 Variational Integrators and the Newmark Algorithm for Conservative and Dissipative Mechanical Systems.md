# Variational Integrators and the Newmark Algorithm for Conservative and Dissipative Mechanical Systems
把 Newmark Algorithm 用 Variational Integrators 的方式构建成一个优化问题，即 Incremental Potential。

证明了 Newmark Algorithm 本身也是满足变分原理的。

## Variational Integrators
[Slide: Discrete Variational Mechanics - Benjamin Stephens](https://www.cs.cmu.edu/~bstephe1/present/variational.ppt)

Main Idea
- Equations of motion are derived from a variational principle
- Traditional integrators discretize the equations of motion
- Variational integrators discretize the variational principle

### 连续形式的 Lagrangian
**用变分原理根据动势泛函求得的 Trajectory 和 Newton-Euler Equation 是等价的**

定义 Lagrangian （动势）

$$L(q, \dot{q}) = T(q, \dot{q}) - U(q)$$

- $q\in Q$: Configuration space
- $q, \dot{q} \in TQ$: Tangent space
- $T(q, \dot{q})$: 动能
- $U(q)$: 势能

Principle of Least Action 告诉我们，实际的 trajectory $q^*$ 是最小化 Langrangian 关于时间的积分的 q

$$\int_{0}^{T}L(q^*(t), \dot{q}^*(t), t)dt \leq \int_{0}^{T}L(q^*(t) + \epsilon q(t), \dot{q}^*(t) + \epsilon q^*(t), t)dt$$

而 Hamilton's Principle 进一步把该定理表述为**变分原理**：任意两点之间真实运动路线的动势的时间积分为驻值。

$$\delta \int_{0}^{T}L(q(t), \dot{q}(t), t)dt = 0$$

求解上式等价于求解 Euler-Lagrange Equation

$$\frac{\partial L}{\partial q} - \frac{d}{dt}[\frac{\partial L}{\partial \dot{q}}] = 0$$

如果把 $L(q, \dot{q}) = T(q, \dot{q}) - U(q)$ 带入 Euler-Lagrange Equation，则有

$$\begin{aligned}
    &\frac{\partial L}{\partial q} - \frac{d}{dt}[\frac{\partial L}{\partial \dot{q}}]\\
    =&\frac{\partial T}{\partial q} - \frac{\partial U}{\partial q} - \frac{\partial^2T}{\partial\dot{q}\partial \dot{q}}\ddot{q} - \frac{\partial^2T}{\partial\dot{q}\partial {q}}\dot{q}\\
    =&0
\end{aligned} $$

而这在本质上和 Newton-Euler Equation 一致（啊？？

$$M(q)\ddot{q} + C(q, \dot{q}) + G(q) = 0$$

### 离散形式 Lagrangian
$$\begin{aligned}
    \int_{T}^{T+h} L (q(t), \dot{q}(t))dt &\approx L_d(q_k, q_{k+1}, h)\\
    &= L(q_k, \frac{q_{k+1}- q_k}{h})h
\end{aligned}$$

Principle of Least Action 的形式变成了

$$\delta \sum_{k=0}^{N-1}L_d = 0$$

可以用 Discrete Euler-Lagrange Equation

$$\begin{aligned}
    &D_2L_d(q_{k-1}, q_k, \Delta t) + D_1L_d(q_{k}, q_{k+1}, \Delta t) = 0\\
    &\frac{\partial}{\partial q_k}\left[L\left(q_{k-1}, \frac{q_k - q_{k-1}}{h}\right)h\right] + \frac{\partial}{\partial q_k}\left[L\left(q_{k}, \frac{q_{k+1} - q_{k}}{h}\right)h\right] = 0\\
    &\tiny\frac{\partial L}{\partial q}\left(q_{k-1}, \frac{q_k - q_{k-1}}{h}\right)h + \frac{\partial L}{\partial\dot{q}}\left(q_{k-1}, \frac{q_k - q_{k-1}}{h}\right) + \frac{\partial L}{\partial q}\left(q_{k}, \frac{q_{k+1} - q_{k}}{h}\right)h + \frac{\partial L}{\partial\dot{q}}\left(q_{k}, \frac{q_{k+1} - q_{k}}{h}\right) = 0
\end{aligned}$$

> $D_1L$ denotes the derivative of $L$ with respect to its first slot.

记上式为 $f(q_{k+1}) = D_2L_d(q_{k-1}, q_k, \Delta t) + D_1L_d(q_{k}, q_{k+1}, \Delta t) = 0$

这时可以直接用 Newton Method 求根

$$q_{k+1}^{i+1} = q_{k+1}^i - \frac{f(q_{k+1}^i)}{\nabla f(q_{k+1}^i)}$$

### Variational Integrator
即不使用牛顿欧拉方程，而是通过变分法求解动势泛函来求解 dynamics system 状态变化的方法。

- 列出离散形式动势泛函 $L_d$
- 列出离散形式欧拉-拉格朗日方程 f(q_{k+1})
- 求欧拉-拉格朗日方程关于 Configuration 的梯度，本质上是求动势关于 $q$ 和 $\dot{q}$ 的梯度。
- 用牛顿法求欧拉-拉格朗日方程的解

## Incremental Potential
