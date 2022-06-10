# Affine Body Dynamics: Fast, Stable & Intersection-free Simulation of Stiff Materials.
总体上是对 Rigid-IPC 的改进。Rigid IPC 在实现上就有着一个核心矛盾，在讨论的模型已经变为 rigid 的情况下，依然沿用 IPC 的总体框架，以至于即使有现成的仿射变换，也要把仿射变换拆散，在用原本的框架求解。

## 仿射变换参数化
对每个物体 $b$，将他在某一时刻 $t$ 的位置用旋转矩阵 $A_b(t)\in \mathbb{R}^{3\times 3}$ 和平移向量 $p(t)\in \mathbb{R}^3$ 来表示。

将旋转矩阵和平移向量放在一起组成 12 维向量作为每个 body 的 configuration $q = (p^T, a_1^T, a_2^T, a_3^T)^T\in\mathbb{R}^12$。

对于形变物体来说，IPC 需要针对其每一个材质点 material point $k$ 计算动能和形变势能，对此本文和 RigidIPC 处理差不多，给每个 vertex 一个 body frame 的坐标 $\bar{x}_k$，需要计算能量的时候用仿射变换转换到世界坐标。

$$x_k = A_b\bar{x}_k + p_b = J(\bar{x}_k)q\\
\dot{x}_k = \dot{A}_b\bar{x}_k + \dot{p}_b = J(\bar{x}_k)\dot{q}$$

其中 $J(\bar{x}) = [I_3, I_3\bigotimes \bar{x}]$。

单个 material point 的动能可以更进一步表示为

$$E_{Kinetic} = \frac{1}{2}\dot{q}^TM\dot{q},~~~M = \int_\Omega\rho J(\bar{x})^TJ(\bar{x})d\Omega$$

整个 Affine Body Dynamics 系统的基本力学方程为对每个 material point 都有

$$M\ddot{q} = -\nabla V(q) + f$$

实际计算中，外力是施加在所有 material point 上外力之和 $f = \sum_k J(\bar{x}_k)^Tf_k$。