# Logic-Geometric Programming: An Optimization-Based Approach to Combined Task and Motion Planning

LGP 的基础工作。

求解的问题目标并不是用 symbolic goal desciption 表示的，而是通过一个基于最终 geometric state 所构建的 cost function 给出的，从而能够通过优化的方式求解 TAMP 问题。

这种方式使得本文提出的 Planner 可以处理一些现有方案难以解决的问题，例如文中给出的示例问题：给出一堆物体和一个杯子，把杯子堆到尽量高的位置。

Logic-Geometric Program 基本含义：
- $\mathcal{L}$: Logic。可以理解为是一个包含 predicates 的逻辑表达式
- 优化目标 $x\in \mathcal{X}$，及其对应的目标函数 $f(x)$。$x$ 是一个连续量。对于一般的工具操作任务，$x$ 是物体以及机器人以及其之间交互的完整路径。
- constraint $\alpha \in \mathcal{L}$: 包含 geometric 和 differential constraints
- constraint function $g(x|\alpha): \mathcal{X}\rightarrow \mathbb{R}^{d(\alpha)}$, $h(x|\alpha): \mathcal{X}\rightarrow \mathbb{R}^{e(\alpha)}$

$$\min_{x,\alpha}f(x, \alpha) \text{ s.t. } \alpha\models K, g(x,\alpha)\leq 0, h(x, \alpha) = 0$$