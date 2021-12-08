# Collision-Free Kinematics for Redundant Manipulator in Dynamic Scenes using Optimal Reciprocal Velocity Obstacles
移动问题的实时避障，可以在避障的同时维持 IK 求解。

## Algorithms
IK 的基本表述为 

$$\theta_{1,2,...,n} = f^{-1}(\xi_{E})$$
- $\theta$: Joint 角度
- $\xi$: End effector pose

### Inverse Jacobian Algorithm
KDL 主要实现的算法。

对于机械臂，有

$$dx = Jdq$$
$$dq = J^{-1}dx$$

每次求出 End Effector 与目标之间的差距，然后通过

$$q_{k+1} = q_{k} + J^{-1}Err$$

迭代更新 Joint Configuration。其中 $J^{-1}$ 用的是 Moore Penrose pseudoinverse。

### Sequential Quadratic Programming
把 Inverse Kinematics 看作一个优化问题。

$$\begin{aligned}
\argmin_{\theta\in\mathbb{R}^n} ~~&(\theta_{seed} - \theta)^T(\theta_{seed} - \theta)\\
\text{s.t.} ~~&g(\theta)\leq 0
\end{aligned}$$

$\theta_{seed}$ 即起始位置，$g(\theta)\leq 0$ 也包含了 end effector 位置要求。

### Reciprocal Velocity Obstacles

