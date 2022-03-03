# Intersection-free Rigid Body Dynamics
将 IPC 应用在刚体上。

## Input
与 IPC 相比，Rigid Body 情形的一大改变就是输入不同，由于不需要考虑形变，所以一个 object 的位置信息只需要一个位移向量 $q_i$ 和一个旋转向量 $\theta_i\in\mathbb{R}^3$。

每个物体包含 $k_i$ 个顶点，这些顶点的位置由 $3 \times k_k$ 矩阵 $\hat{X}_i$ 描述。每个物体还包括其 mesh faces $F_i$，质量 $m_i$，惯性参考系 $I_i$。

刚体位置和旋转到每个 顶点 的世界坐标可以如下得到

$$\phi_i(\theta_i, q_i) = \mathcal{R}(\theta_i)\hat{X}_i + q_i$$

## Rigid Body Incremental Potential
原本 IPC 中 Incremental Potential 的形式是

$$B_{t}(x) = E(x, x^t, v^t) + \kappa\sum_{k\in\mathcal{C}}b(d_k(x))$$