# Hierarchical Planning for Long-Horizon Manipulation with Geometric and Symbolic Scene Graphs
*该文章里的 horizon 可以理解为“范围”，long-horizon 也就是长时间范围内的意思。*

采用的方法依然是基于视觉的。核心点在于，在机器人领域，直接对 sensor data（视觉是很核心的传感数据）应用深度学习的方法，缺乏对于需要对一段时间的行为进行规划（TAMP任务）的能力。本文虽然还是基于视觉，但是为了让视觉方法能够用于 Task Plan，额外构建了了一个 Symbolic Scene Graph。个人感觉和 kinematic tree 已经非常类似了。

使用的核心技术是 Graph Neural Networks（GNNs），而不是通常的 TAMP 方法使用的逻辑搜索。

**neuro-symbolic hybrid model**

**Regression Planning Network (RPN)**

## 符号
symbol | meaning
-- | --
$\mathcal{O}$ | 视觉信息空间
$\mathcal{G}$ | 目标，包含最终的目标和每一步的 subgoal
$\mathcal{A}$ | action 空间
$\mathcal{Z}$ | 所有 predicate
$s_g$ | Geometric Scene Graph
$s_s$ | Symbolic Scene Graph

## Geometric Scene Graph
$s_g$ 表示一个 Geometric Scene Graph

每个节点是一个物体的 6D Pose，每条边是两个物体之间的空间关系。

Geometric Scene Graph 是通过 6D Pose 估计模型 $f_p$ 从视觉信息 $o\in \mathcal{O}$ 中构建出来的。$s_g = f_p(o)$

个人感觉这个 Geometric Scene Graph 啥也不是，把每个物体的 6D 存下来而已。

## Symbolic Scene Graph
$s_s$ 表示一个 Symbolic Scene Graph。节点是物体，而边是 predicates 表示的物体之间关系。

Symbolic Scene Graph 是根据 Geometric Scene Graph 构建的，通过一个 symbol mapping function $f_m$，来计算 Symbolic Scene Graph $s_s = f_m(s_g)$。

## Method
### Task Plan Model
$\phi: \mathcal{Z} \times \mathcal{G} \rightarrow \mathcal{G}$，用于根据 symbolic scene graph $s_s\in \mathcal{Z}$ 和最终目标 $g\in \mathcal{G}$ 生成 subgoal $\bar{g} \in \mathcal{G}$。

文章中使用的 Task Plan Model 是基于[Regression Planning Network](2019&#32;Regression&#32;Planning&#32;Network.md)

### Motion Generation Model
$\psi:\mathcal{O}\times \mathcal{G} \rightarrow \mathcal{A}$。

## 问题
- 方法的核心就是用 Neuro-Symbolic Task Planning （更准确地说是使用 Regression Planning Network）来做 Task Plan
- 由于整个场景是基于视觉构建的，决定了物体之间的关系需要非常简单，实际上文章中的场景就只是位置关系，不包含更复杂的运动学信息。
- 也正以为物体之间只有位置关系，才能直接做 Motion Plan，因为需要的 Motion 只是，把xx物体拿到xx位置。