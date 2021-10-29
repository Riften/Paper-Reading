# Logic-Geometric Programming: An Optimization-Based Approach to Combined Task and Motion Planning

LGP 的基础工作。

> Instead of trying to pull geometry into logic representations, we try to pull logic into mathematical programming.

求解的问题目标并不是用 symbolic goal desciption 表示的，而是通过一个基于最终 geometric state 所构建的 cost function 给出的，从而能够通过优化的方式求解 TAMP 问题。

这种方式使得本文提出的 Planner 可以处理一些现有方案难以解决的问题，例如文中给出的示例问题：给出一堆物体和一个杯子，把杯子堆到尽量高的位置。

## Logic-Geometric Program 
总体的 LGP 问题定义
- $\mathcal{L}$: Logic。可以理解为是一个包含 predicates 的逻辑表达式
- 优化目标 $x\in \mathcal{X}$，及其对应的目标函数 $f(x)$。$x$ 是一个连续量。对于一般的工具操作任务，$x$ 是物体以及机器人以及其之间交互的完整路径。
- constraint $\alpha \in \mathcal{L}$: 包含 geometric 和 differential constraints
- constraint function $g(x|\alpha): \mathcal{X}\rightarrow \mathbb{R}^{d(\alpha)}$, $h(x|\alpha): \mathcal{X}\rightarrow \mathbb{R}^{e(\alpha)}$

$$\min_{x,\alpha}f(x, \alpha) \text{ s.t. } \alpha\models K, g(x,\alpha)\leq 0, h(x, \alpha) = 0$$

具体到本文中的机器人操作物体的 LGP 问题定义
- $\mathcal{X} = \mathbb{R}^n\times SE(3)^m$: 一个 n 轴机器人和 m 个物体的状态空间。
- $x(0)$: 初始状态
- 任务描述 $\mathcal{L}$，其中任务的初始状态为 $s_0\in \mathcal{L}$。需要注意的事这里的 $s_0$ 区别于 $x(0)$，$s_0$ 是一个由命题表述的初始状态，是 symbolic 的。
- $T$: action operators. operator 可以转变 symbolic 的状态 $s_k = succ(a_k, s_{k-1})$
- 时间点序列 $t_k\in[0, T], k=1,2,...,K$
