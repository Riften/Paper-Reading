# Intersection-free Rigid Body Dynamics
将 IPC 应用在刚体上。

## Input & Output
与 IPC 相比，Rigid Body 情形的一大改变就是输入不同，由于不需要考虑形变，所以一个 object 的位置信息只需要一个位移向量 $q_i$ 和一个旋转向量 $\theta_i\in\mathbb{R}^3$。

每个物体包含 $k_i$ 个顶点，这些顶点的位置由 $3 \times k_k$ 矩阵 $\hat{X}_i$ 描述。每个物体还包括其 mesh faces $F_i$，质量 $m_i$，惯性参考系 $I_i$。

刚体位置和旋转到每个 顶点 的世界坐标可以如下得到

$$
\begin{aligned}
\phi_i(\theta_i, q_i) = \mathcal{R}(\theta_i)\hat{X}_i + q_i\\
\phi_i: \mathbb{R}^3\times \mathbb{R}^3 \rightarrow \mathbb{R}^{3\times k_i}   
\end{aligned}
$$
- $\mathcal{R}(\theta)$: 本质上是一个旋转矩阵，将顶点在 local frame 的坐标映射到和 world frame 平行的 frame 中的坐标。

刚体的模拟输入 $(\theta^t, q^t)$，输出 $(\theta^{t+1}, q^{t+1})$

## Rigid Body Incremental Potential
原本 IPC 中 Incremental Potential 的形式是

$$B_{t}(x) = E(x, x^t, v^t) + \kappa\sum_{k\in\mathcal{C}}b(d_k(x))$$

在刚体情况下，碰撞势能依旧由 Barrier 给出，摩擦也不变。主要变化的是动能项。

令 $Q_i^t=\mathcal{R}(\theta_i^t)$ 为第 $i$ 个刚体在 timestep $t$ 的旋转矩阵，$J_i$ 为其 inertial matrix，则旋转动能为

$$\frac{1}{2}\text{tr}(\dot{Q}_iJ_i\dot{Q}_i^T)$$

接下在需要在这个基础上构建 IP 表达式

类似 $\hat{x} = x^t + hv^t + h^2M^{-1}f_e$，定义 $\tilde{Q}_i^t = Q_i^t + h\dot{Q}_i^t + h^2[\tau_i]J^{-1}$
- $\tau$ 为外力矩。
$$[\tau] = \left[\begin{array}{ccc}
    0 & -\tau_z & \tau_y\\
    \tau_z & 0 & -\tau_x\\
    -\tau_y & \tau_x & 0
\end{array}\right]$$