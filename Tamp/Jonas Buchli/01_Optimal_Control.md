# Optimal Control
Optimal Control 问题的一般形式
$$\begin{aligned}
\min_\mu\Large[\normalsize \Phi(x(N)) &+ \sum_{n=0}^{N-1}L_n(x(n), u(n))\Large]\\
\text{s.t.~~~~} x(n+1) &= f(x(n), u(n))\\
x(0) &= x_0\\
u(n,x) &= \mu(n,x)
\end{aligned}$$

- $L$ 为 cost function, $x(n+1) = f(x(n), u(n))$ 为 system dynamics. 在模拟环境中，通常由动力学模型作为 system dynamics.
- $f$ 是 system dynamic，在机器人相关问题中通常是动力学模型。
- $\mu$ 是优化目标，policy。对于 Model Base RL 算法，$\mu$ 通常是一个神经网络。optimal control 问题的目标是求解 $\mu^* = \argmin_{u} J$

Value Function: 某个状态的值函数被定义为从该状态到目标状态的 accumulated cost
$$V^\mu(n, x) = \alpha^{N-n}\Phi(x_N) + \sum_{k=n}^{N-1}L_k(x_k, u_k)$$

Bellman Equation: 
$$V_\mu(n,x) = L_n(x,u_n) + \alpha V^\mu(n+1, f_n(x,u_n))$$

## Sequential Quadratic Programming
是一种求解 Nonlinear Programming Problem 的通用近似方法。

$$\begin{aligned}
    \min_x~~~&  f(x) \\ 
    \text{s.t.}~~~&  g(x) \leq 0, h(x) = 0
\end{aligned}$$

对于优化问题而言，两种情况下时有确定解的
- Linear Programming
- Quadratic Programming: $f$ 是二次函数，剩下的 constraint 是线性函数

SQP 的意思就是用一系列二次函数来近似 $f(x)$，总体过程是
- 猜测一个解 $\tilde{x}_0$
- 在该点处用 二阶泰勒展开 近似原方程，用一阶泰勒展开近似 constraint
- 通过 QP Solver 得到下一个解 $\tilde{x}_1$
- 迭代运行

对于 convex problem，SQP 可以得到最优解。

## Sequential Linear Quadratic Programming (SLQ)
Sequential Quadratic Programming 求解的 NLP 问题和 Optimal Control 问题之间还有一定差距。

对于 cost function 和 system dynamics 为 non-linear 的情况，可以用 SLQ 近似求解。

$$\begin{aligned}
\min_\mu\Large[\normalsize \Phi(x(N)) &+ \sum_{n=0}^{N-1}L_n(x(n), u(n))\Large]\\
\text{s.t.~~~~} x(n+1) &= f(x(n), u(n))\\
x(0) &= x_0\\
u(n,x) &= \mu(n,x)
\end{aligned}$$

基本过程
1. 猜测一个初始解 $\mu^0(n,x)$
2. "Roll out": 应用 policy 得到 state trajectory $X_k = \{x(0), ..., x(N)\}$，和 input trajectory $U_k = \{u(0), ... , u(N-1)\}$
3. 在 $n=m$ 处，用一个 quadratic function 近似 Value Function $\tilde{V}^\mu(x_m, u_m)$
4. 求解 Bellman Equation $u^*(m, x_m) = \argmin_{u_m}[L_m(x_m, u_m) + \alpha \tilde{V}(x_{m+1}, u_{m+1})]$，得到在 $m$ 处最小化 Value Function 的 control policy
5. 从 $m=N-1$ 到 $m=0$ 执行 3，4。可以得到一个优化后的 control input，然后更新 $\mu^{k+1} = \mu^k + \alpha_k \delta \mu^k$
6. 重复 2-5 直到满足停止条件。

SLQ 算法和 SQP 很类似，只是由于 constraint 是非线性的，所以不能用简单地 QP Solver 更新参数。

## Iterative Linear Quadratic Controller (ILQC)
ILQC 是 SLQ 的一种，通过把原来的非线性问题转化为迭代解决的一系列 linear dynamics and quadratic cost 问题。

- 初始化：初始化一个合理的 policy $\mu$
- Roll-Out: 通过 forward-ingetration，按照 nonlinear system dynamics 执行 $\mu$，得到 trajectory $\bar{u}_n^{(i)},\bar{x}_n^{(i)}$ for $n=1,...,N$
  - $$x_{n+1} = f_n(x_n, u_n)$$
- Linear-Quadratic Approximation: 对每一个 $(\bar{u}_n^{(i)},\bar{x}_n^{(i)})$对，构建一个局部的 linear-quadratic 近似
  - 目的是在 trajectory 的局部，将 dynamic 近似成线性，将 cost 近似成 quadratic

## Linear Quadratic Regulator (LQR)
