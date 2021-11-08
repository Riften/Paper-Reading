# KOMO - Newton Methods for $k$-order Markov Constrained Motion Problems
KOMO 的核心目的在于将 Optimizer 和 Motion Problem 的代码分离开，用通用的 Optimizer 求解任意的 Motion Problem。

## 优化问题形式
定义 $x_t\in \mathbb{R}^n$ 为 joint configuration。正常来说，系统的状态 state 定义为 $(x_t, \dot{x}_t)$，但是 KOMO 只使用 $x_t$ 作为 state。trajectory 被定义为 $x$ 的序列 $x_{0:T} = (x_0, ..., x_T)$。优化问题形式为 

$$\begin{aligned}
\min_{x_{0:T}}&  &\sum_{t=0}^Tf_t(x_{t-k:t}) + \sum_{t, t'}k(t,t')x_t^Tx_{t'}\\
\text{s.t.}&  &\forall_t: g_t(x_{t-k:t})\leq 0, h_t(t_{t-k:t}) = 0
\end{aligned}$$

优化问题将考虑 $x_{t-k:t} = (x_{t-k}, ..., x_t )$ 一共 $k+1$ 个连续 configuration。