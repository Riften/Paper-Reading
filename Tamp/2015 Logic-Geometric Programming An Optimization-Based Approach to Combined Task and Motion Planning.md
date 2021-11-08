# Logic-Geometric Programming: An Optimization-Based Approach to Combined Task and Motion Planning

LGP 的基础工作。

> Instead of trying to pull geometry into logic representations, we try to pull logic into mathematical programming.

求解的问题目标并不是用 symbolic goal desciption 表示的，而是通过一个基于最终 geometric state 所构建的 cost function 给出的，从而能够通过优化的方式求解 TAMP 问题。

这种方式使得本文提出的 Planner 可以处理一些现有方案难以解决的问题，例如文中给出的示例问题：给出一堆物体和一个杯子，把杯子堆到尽量高的位置。

**优化问题是直接在已经有 action sequence 的情况下进行的，文章的突破在于优化可以对整个 solution 进行，而不是以 primitive action 为单位进行**

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
- 优化目标

$$\min_{x, a_{1:K}, s_{1:K}, t_{1:K}}\int_0^T c(x(t), \dot{x}(t), \ddot{x}(t))dt + \psi(x(T))$$
  
- - $c$: Cost Function，是一个机器人运动路径和加速度等相关的函数，最终目的是最高效。
  - $\psi$: 对最终 configuration 的 Evaluation。通常来说 evaluation 应该是对 goal 状态的一个描述，然而在 LGP 问题定义中，evaluation 也出现在了 cost 函数中，这是因为 LGP 的 goal 可以是可优化的，例如把物体堆得尽量高。
- Constraints (Subject To):
  - $\forall k=1:K$, $s_k = succ(a_k, s_{k-1})$ : 在优化阶段，Action Sequence 是已经确定的，由 action sequence 给出的 kinematic state 转换关系也称为了第一个 constraint.
  - $s_K \models \mathfrak{g}$: goal test
  - $\forall_{k=1}^K h_{switch}(x(t_k) | a_k, s_{k-1}) = 0$
  - $\forall_{k=1}^K g_{switch}(x(t_k) | a_k, s_{k-1}) \leq 0$: contact switch 前后状态一致性，这里只有 $x$ 而不包含其导数也是因为再发生 contact switch 的时候往往是不可导的。这里的 constraint 信息由 action 描述，而 action 是一个 symbolic 概念，这就要求 LGP 使用的符号系统可以在 symbolic 层面描述 (contact) switch。
  - $\forall_{t\in[0, T]} h_{path}(x(t), \dot{x}(t) | s_{k(t)}) = 0$
  - $\forall_{t\in[0, T]} g_{path}(x(t), \dot{x}(t) | s_{k(t)}) \leq 0$ : 表示在 contact 不变的阶段内，constraint 是由 state 决定的。state 定义了 kinematic 状态，而 kinematics 本质上就是对系统可以执行的 motion 的描述。同时 state 是 symbolic 的，这就需要 LGP 使用的符号系统可以直接在 symbolic 层面描述 kinematic 信息。

## Effective End Space