# A Direct Method for Trajectory Optimization of Rigid Bodies Through Contact
Collocation (Direct) Trajectory Optimization。考虑非弹性碰撞和库伦摩擦的力交互，在求解 contact constraint forces 的同时优化 trajectory 使其满足 constraints 的互补条件（类似用线性互补问题求解碰撞）。

问题：刚体的 trajectory optimization，考虑非弹性碰撞和摩擦。

## autonomous hybrid dynamical system
碰撞带来的一大问题就是其瞬时性，这可能导致极大的瞬时力以及速度的快速变化，这使得动力学的可微方程依赖于更小的 time step 来保证连续性。

一种可行的的方案是，直接将碰撞近似为离散的事件，直接导致速度的跃迁，而不是通过可微的动力学方程来描述碰撞。这样简化的动力学模型被称为 autonomous hybrid dynamical system.

这样的系统一般将一个 trajectory 划分成不同的 mode，然后在保持 mode schedule 不变的前提下进行搜索。

autonomous hybrid dynamical system 模型对于持续接触的 contact 关系并不能很好的处理，例如灵巧手，或者直接用于 grasp / manipulate 的 planning。**这些问题中的 contact 过于频繁和复杂，以至于用特定的 mode 来近似 contact 的做法反而更加复杂，而且输入的改变对 mode schedule 的影响更加显著，在特定 mode 顺序下的搜索空间变得特别小**。

## Method
对于这一类问题中的 contact 关系，通常是转化成 linear or nonlinear complementarity problems 求数值解。

而本文的基本逻辑就是 **将求解 contact 的互补问题直接整合到 collocation method 的优化问题中**。最终问题形式是一个 Mathematical Program with Complementarity Constraints (MPCC)，并用 Sequential Quadratic Programming (SQP) 求解。

## Contact Dynamics as Complementarity Problem
