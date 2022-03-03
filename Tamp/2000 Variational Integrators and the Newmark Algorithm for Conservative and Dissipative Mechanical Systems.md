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

定义 Lagrangian （动势，拉格朗日量）

$$L(q, \dot{q}) = T(q, \dot{q}) - U(q)$$

- $q\in Q$: Configuration space
- $q, \dot{q} \in TQ$: Tangent space
- $T(q, \dot{q})$: 动能
- $U(q)$: 势能

Principle of Least Action 告诉我们，实际的 trajectory $q^*$ 是最小化 Langrangian 关于时间的积分的 q

$$\int_{0}^{T}L(q^*(t), \dot{q}^*(t), t)dt \leq \int_{0}^{T}L(q^*(t) + \epsilon q(t), \dot{q}^*(t) + \epsilon q^*(t), t)dt$$

这里的 $S=\int_{0}^{T}L(q(t), \dot{q}(t), t)dt$ 称为 action （作用量）。

Hamilton's Principle 进一步根据**变分原理**，S取极值的必要条件是：

$$\delta \int_{0}^{T}L(q(t), \dot{q}(t), t)dt = \int_{0}^{T}\left[\frac{\partial L}{\partial q}\delta q + \frac{\partial L}{\partial \dot{q}}\delta{\dot{q}}\right]dt = 0$$

上式是必要条件的积分形式，S取极值的必要条件的微分形式是 Euler-Lagrange Equation

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
Newmark Method 能够求解的基本假设是线性假设 $C\dot{q} + Kq$，但是实际上的阻力和内力关系往往没那么简单。

本文的一大部分都在证明 Newton-Euler Equation 和 Lagrange 变分的一致性，这里相当于希望把 Newton-Euler Equation 转化成一个变分条件。Newton-Euler Equation 实际上是 *力为0*,而和力本质上是能量关于 $q,\dot{q}$ 的导数，即力关于 $q,\dot{q}$ 的积分就是能量变化。

所以这里的基本思路是
- 通过 Newmark Method 中使用中值定理得到的近似，将 Newton-Euler Equation 中除了 $q$ 以外的变量去掉
- Newton-Euler Equation 关于 $q$ 的积分等价于 $\int L(q,\dot{q}, t)dt$，该积分关于 $t$ 的导数就是 $f(q)$ ,某状态下的总能量。
- 用最优化的方法求 $\argmin_q f(q)$

考虑更通用的的 dynamic equation 形式

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

在位移增量关系中，可以看作有两部分，一部分直接用上一阶段的 $\{q,\dot{q}\}_k$得到，另一部分则需要未知的$\ddot{q_{k+1}}$。

记
$$q^{pre}_{k+1} = q_k + h\dot{q}_{k} + \frac{h^2}{2}[(1-2\beta)\ddot{q}_{k}]$$

这样就把 $q_{k+1}$ 分成了两部分，由上一阶段可以直接推得的 $q^{pre}_{k+1}$ 和需要 $\ddot{q}_{k+1}$ 才能算出来的部分

$$q_{k+1} = q^{pre}_{k+1}+\beta h^2 \ddot{q}_{k+1}$$

原本的 Newmark Method 中，先通过 Newton-Euler Equation 算出来 $q_{k+1}$，然后再算 $\dot{q}_{k+1}$ 和 $\ddot{q}_{k+1}$。能这样直接求解的原因是 Newmark Method 假设阻力和内力都是线性的，所以可以直接求解线性方程。

而在这里 $f^{int}(q,\dot{q})$ 包含了 Newmark Method 中的 $C\dot{q} + Kq$，而且不再保证线性。这让该式能够表示更复杂的 dynamic 关系，但是无法直接通过求解线性方程求解。

为了求解这个更通用的 dynamic equation 得到 $q_{k+1}$，首先需要找到 $f^{int}_{k+1}$ 和 $q_{k+1}$ 之间的关系，来替代原本的线性假设 $C\dot{q} + Kq$ 的作用。

而本文采用的关系就是 **effective incremental potential**：

首先回顾这里的内力的定义是 $f^{int}(q, \dot{q}) = \frac{\partial V(q)}{\partial q} + \frac{\partial \psi(q, \dot{q})}{\partial \dot{q}}$，即保守势和耗散势关于 configuration 的微分，保守势本身就只于 $q$ 有关，所以剩下需要把找到一个耗散势关于 $q$ 的线性近似，这里采用的近似如下

$$\begin{aligned}
    V_k(q_{k+1}) &= V(q_{k+1}) + h\psi\left(q_{k+\sigma}, \frac{q_{k+1} - q_k}{h}\right),\\
    q_{k+\sigma} &= (1-\sigma)q_k + \sigma q_{k+1},~~~~\sigma\in[0,1]
\end{aligned}$$

> 为啥这么取近似？？

这样就有了 $f^{int}$ 和 $q$ 之间的直接关系

$$f^{int}_{k+1} = \frac{\partial V_k(q_{k+1})}{\partial q_{k+1}}$$

当 $h\rightarrow 0$ 的时候上式收敛到 $\frac{\partial V(q)}{\partial q} + \frac{\partial \psi(q, \dot{q})}{\partial \dot{q}}$。

根据 $q_{k+1} = q_k + h\dot{q}_k + \frac{h^2}{2}\left[(1-2\beta)\ddot{q}_k + 2\beta \ddot{q}_{k+1}\right]$ 有 $\ddot{q}_{k+1} = \frac{q_{k+1}-q^{pre}_{k+1}}{\beta h^2}$，加上得到的内力与 $q_{k+1}$ 之间的直接关系，成功的去掉了 Newton-Euler Equation 中除了 $q$ 以外的变量：

$$\begin{aligned}
    M\frac{q_{k+1}-q^{pre}_{k+1}}{\beta h^2} + f^{int}_{k+1} = f^{ext}_{k+1}\\
    M\frac{q_{k+1}-q^{pre}_{k+1}}{\beta h^2} + \frac{\partial V_k(q_{k+1})}{\partial q_{k+1}} = f^{ext}_{k+1}
\end{aligned}$$

> 上式的含义：本质上是 Newton-Euler Equation 的一个近似，而 Newton-Euler Equation 和**动势取极值的必要条件的微分形式 Euler-Lagrange Equation**是一致的。

上式相当于一个隐式的 Newton-Euler Equation，同时本身就是一个 Euler-Lagrange Equation。即上式对所有 t 恒成立 $\Leftarrow$ S 取极值 $\Leftrightarrow$ 得到实际的 trajectory。

但是上式并不好求，另一种思路是求积分形式的 $\delta S = 0$，即下式：

$$\frac{1}{2\beta h^2}(q_{k+1}-q^{pre}_{k+1})^TM(q_{k+1}-q^{pre}_{k+1}) + V_k(q_{k+1}) - f_{k+1}^{ext}q_{k+1}$$

Lagrange 的变分原理告诉我们，$q^*$ 应该是积分的驻点，即原函数值应该不变。

那么该 Newton-Euler Equation 对应的 Euler-Lagrange Equation 可以是

$$f(q_{k+1}) = \frac{1}{2 h^2}(q_{k+1}-q^{pre}_{k+1})^TM(q_{k+1}-q^{pre}_{k+1}) +\beta V_k(q_{k+1}) -\beta f_{k+1}^{ext}q_{k+1}$$

对应的 integrator 可以通过求解

$$\argmin_{q_{k+1}} f(q_{k+1})$$

得到。

> Incremental Potential 给出了一个通用的直接通过求能量函数极值的 integrator。在近似动能的时候利用了 Newmark Method 使得近似比线性近似更精确，并且给出了包含耗散势能和保守势能的近似 $V_k(q_{k+1}) = V(q_{k+1}) + h\psi\left(q_{k+\sigma}, \frac{q_{k+1} - q_k}{h}\right)$