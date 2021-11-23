# Inertia Matrix
## Joint-Space Inertia Matrix
- [Scholarperia: Robot Dynamics](http://www.scholarpedia.org/article/Robot_dynamics)
- [UIUC Planning: Inertia Matrix](http://planning.cs.uiuc.edu/node691.html)

为了求解 Joint-Space 的运动方程所定义的惯性矩阵，不是物体固有属性，会随着物体的运动而变化。

$$M(q)$$

对于基本运动方程

$$M(q)\dot{v}+c(q,v) = \tau + J^Tf\\
M(q)\dot{v} = \tau + J^Tf - c(q,v)$$

Joint Space 内受到的合力为 $\tau_{sum} = \tau + J^Tf - c(q,v)$，这是一个 $(n_v\times 1)$ 的向量。


## Mujoco 中 Joint-Space Inertia Matrix 的使用
MuJoCo 中转轴惯性矩阵是以 Sparse 的形式存储的，并且提供了其 $LDL^T$ 分解的各个参数。通常并不需要直接拿到完整的 Matrix，而只需要调用 MuJoCo 提供的接口，包括
- mj_fullM
- mj_mulM(model, data, res, vec): $Mv$
- mj_mulM2(model, data, res, vec): $M^{\frac{1}{2}}v$
- mj_addM


## 传统的惯性参数
在动力学系统中，惯性参数有三种

**质量 Mass**: $F = ma$

**转动惯量 Moment of inertia**

 $I = \int_V\rho r^2dV =  mr^2$
- 角加速度 $\alpha = \frac{d\omega}{dt}$
- 力矩 $\tau = r\times F$
- $\tau = I\alpha$

**惯性张量**

对于一个坐标系 $Q_{xyz}$，其中的一个物体转动惯量为

$$I = \left[
    \begin{array}{c}
    I_{xx} & I_{xy} & I_{xz} \\
    I_{yx} & I_{yy} & I_{yz} \\
    I_{zx} & I_{zy} & I_{zz}
    \end{array}\right]$$

这里的对角元素值定义为物体绕座标轴旋转的转动惯量

$$I_{xx}=\int (y_2 + z^2)dm\\
I_{yy}=\int (x_2 + z^2)dm\\
I_{zz}=\int (x_2 + y^2)dm$$

非对角元素称为惯量积，定义为

$$I_{xy} = I_{yz} = -\int xydm$$

惯性张量的三个特征向量相互垂直，称为惯量主轴。对应的特征值是刚体对于惯量主轴的主转动惯量。

 