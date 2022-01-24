# Optimal Control
Optimal Control 问题的一般形式
$$\begin{aligned}
\min_\mu\Large[\normalsize \Phi(x(N)) &+ \sum_{n=0}^{N-1}L)n(x(n), u(n))\Large]\\
\text{s.t.~~~~} x(n+1) &= f(x(n), u(n))\\
x(0) &= x_0\\
u(n,x) &= \mu(n,x)
\end{aligned}$$

$L$ 为 cost function, $x(n+1) = f(x(n), u(n))$ 为 system dynamics. 在模拟环境中，通常由动力学模型作为 system dynamics.

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

