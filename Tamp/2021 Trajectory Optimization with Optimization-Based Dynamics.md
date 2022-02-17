# Trajectory Optimization with Optimization-Based Dynamics
(arxiv, [Robotic Exploration Lab](https://roboticexplorationlab.org/) 的新论文)

## Related Work
（我对这个 related work 感激不尽

本文中把 Trajectory Optimization （Dynamic Involved） 总体划分成了两大类
- Bi-Level Optimization: 也是本文采用的方法。简单说整个求解过程中不仅仅包含一个优化问题，通常包含一个隐式的优化问题来求解一些 dynamic 关系。然后一个上层的 Optimizer，利用底层优化问题的结果（或者梯度），来构建和求解 Trajectory Optimization 问题
- Explicit Approchs: 或者说 Direct Method，直接将 contact dynamics 表示成一个 Linear complementarity problem。