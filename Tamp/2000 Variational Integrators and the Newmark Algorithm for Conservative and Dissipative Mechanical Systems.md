# Variational Integrators and the Newmark Algorithm for Conservative and Dissipative Mechanical Systems
把 Newmark Algorithm 用 Variational Integrators 的方式构建成一个优化问题，即 Incremental Potential。

证明了 Newmark Algorithm 本身也是满足变分原理的。

Integrator 总体的

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
从 Newmark Algorithm 推导的一个用 optimization 进行 integration 的策略。

### Newmark Algorithm
- [ScienceDirect: Newmark Method](https://www.sciencedirect.com/topics/engineering/newmark-method)
- [Wikipedia: Newmark-$\beta$ Method](https://en.wikipedia.org/wiki/Newmark-beta_method)

整个 dynamic system 由以下动力方程决定

$$M\frac{d^2q}{dt^2} + C\frac{dq}{dt} + f^{int}(q) = f^{ext}(t)$$
- $M$ mass matrix
- $C$ damping matrix 阻尼矩阵
- $f^{int}$ internal force
- $f^{ext}$ external force

对于运动系统来说 $q(t)$ 一定是一阶可微的（速度连续），所以满足 Mean Value Throrem （两点之间存在一点的导数等于两点连线的斜率），即对任意 $t, \Delta t$，总存在 $\beta\in(0,1)$，使得

$$q(t+\Delta t) - q(t) = q'(t+\beta\Delta t)\Delta t$$

记 $q_{n+1} = q(t+\Delta t)$, $q_n = q(t)$，$q_\gamma = (1-\gamma )q_n + \gamma q_{n+1}$，$0\leq\gamma\leq 1$，即 $q_\gamma$ 是 $[t,t+\Delta t]$ 之间的一点处的值。

<!--则泰勒展开可以表述为

$$\begin{aligned}
    q_{n+1} &= q_n + q'_n\Delta t + \ q''_n\frac{(\Delta t)^2}{2!} + q'''_n\frac{(\Delta t)^3}{3!} + ...\\
    q'_{n+1} &= q'_n + q''_n\Delta t + \ q'''_n\frac{(\Delta t)^2}{2!} + ...
\end{aligned}$$
-->

则根据微分中值定理把**差分用一阶导数的中值代替**，有（其实就是 $v(t+\Delta t) = v(t) + a\Delta t$）

$$\begin{aligned}
    \dot{q}_{n+1} &= \dot{q}_n + \Delta t \ddot{q}_\gamma\\
    &=\dot{q}_n + \Delta t \left[(1-\gamma) \ddot{q}_n + \gamma\ddot{q}_{n+1}\right] \\
    &=\dot{q}_n +  (1-\gamma)\Delta t \ddot{q}_n + \gamma\Delta t\ddot{q}_{n+1}
\end{aligned}$$

然后对于位移，也用同样地 **导数中值代替差分** 的想法，但是为了和加速度建立联系，用的是二阶导数的中值，记 $q_\beta = (1-2\beta)q_n + 2\beta q_{n+1}$，$0\leq 2\beta\leq 1$，类似速度，有

$$\begin{aligned}
    q_{n+1} &= q_n + \Delta t\dot{q}_n + \frac{1}{2} \Delta t^2 \ddot{q}_\beta \\
    &=q_n + \Delta t\dot{q}_n + \frac{1}{2} \Delta t^2 [(1-2\beta)\ddot{q}_n + 2\beta \ddot{q}_{n+1}]
\end{aligned}$$

> 为什么 $q_{n+1} = q_n + \Delta t\dot{q}_n + \frac{1}{2} \Delta t^2 \ddot{q}_\beta$ ?
> $\ddot{q}_\beta$ 是 $\ddot{q}$ 在 $[t, t + \Delta t]$ 上的中值，等于 $\dot{q}_n$ 和 $\dot{q}_{n+1}$ 关于时间的差分，即$\ddot{q}_\beta = (\dot{q}_n$ + $\dot{q}_{n+1})/\Delta t$，带入原式就是 $q_{n+1} = q_n  + \frac{1}{2} \Delta t(\dot{q}_{n+1} + \dot{q}_n)$。这个等式显然是不一定成立的，但是我们的目的是找到一个足够接近的线性近似，如果 $q$ 是线性的，这个等式就是成立的。

上面两个式子是 **Newmark Algorithm 的核心**，给出了速度和位移的 integration，但是其中都用到了加速度。而加速度可以从动力方程得到

$$\ddot{q}_{n+1}  =  M^{-1}\left[ f^{ext}(t_{n+1}) - C\dot{q}_{n+1} - f^{int}(q_{n+1}) \right]$$

然后上面三个式子可以整理成 $X_{n+1}, \dot{X}_{n+1}, \ddot{X}_{n+1} = F(X_n, \dot{X}_n, t)$ 的形式，也就是我们想要的 integrator 形式：

记 $f^{int}(q) = Kq$，即内力也是关于 configuration 线性的。

对于位移，有
$$\begin{aligned}
    q_{n+1} &= A^{-1}B_n\\
    A &= \frac{M}{\beta(\Delta t)^2} + \frac{\gamma C}{\beta \Delta t} + K \\
    B_n &= f^{ext}(t_{n+1}) + M\left[\frac{q_n}{\beta(\Delta t)^2} + \frac{q'_n}{\beta\Delta t} + (\frac{1}{2\beta}-1)q''_n\right] + C\left[\frac{\gamma q_n}{\beta\Delta t} - q'_n(1-\frac{\gamma}{\beta}) - \Delta t q''_n (1-\frac{\gamma}{2\beta})\right]
\end{aligned}$$

对于速度，有

$$q'_{n+1} = \frac{\gamma(q_{n+1}-q_n)}{\beta\Delta t} + q'_n(1-\frac{\gamma}{\beta}) + \Delta t q''_n(1-\frac{\gamma}{2\beta})$$

对于加速度

$$q''_{n+1} = \frac{q_{n+1} - q_n}{\beta \Delta t^2} - \frac{q'_n}{\beta\Delta t} - (\frac{1}{2\beta} - 1)q''_n$$

### Effective Incremental Potential
考虑简化的 dynamic equation

$$M\ddot{q} + f^{int}(q,\dot{q}) = f^{ext}(t)$$

这里的 internal force 被假设影响两部分势能
- 保守力势能 conservative potential $V(q)$，类似重力势能，能量只于力和力的位移有关，和路径无关
- 耗散势能 dissipative potential $\psi (q,\dot{q})$，例如弹性形变或者弹性碰撞耗散的内能。

内力和这两部分的能量关系是

$$f^{int}(q, \dot{q}) = \frac{\partial V(q)}{\partial q} + \frac{\partial \psi(q, \dot{q})}{\partial \dot{q}}$$

Newmark Scheme 给出了基于中值定理的 $q$，$\dot{q}$，$\ddot{q}$ 的增量关系($h$ 为时间微元 $\Delta t$)：

$$\begin{aligned}
    q_{k+1} &= q_k + h\dot{q}_k + \frac{h^2}{2}\left[(1-2\beta)\ddot{q}_k + 2\beta \ddot{q}_{k+1}\right]\\
    M\ddot{q}_{k+1} + f^{int}_{k+1} &= f^{ext}_{k+1}\\
    \ddot{q}_{k+1} &= \ddot{q}_k + h [(1-\gamma)\ddot{q}_k + \gamma q_{k+1}]
\end{aligned}$$