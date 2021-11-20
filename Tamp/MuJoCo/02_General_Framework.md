# General Framework
基本运动方程

$$M\dot{v}+c = \tau + J^Tf$$

- $M$: 惯性矩阵，$n_V$ 为 DOFs 数量，则 $M$ 尺寸为 $n_V\times n_V$
- $\dot{v}$: 所有自由度速度的一阶导数，也就是加速度， $size=n_V$
- $c$: 一些外在的偏向力，例如重力、地转偏向力、整个座标系在旋转时的离心力。$size=n_V$
- $\tau$：施加的力。$size=n_V$。系统被动承受的力也在这一部分，例如流体或弹簧系统带来的阻力。
- $J$: Constraint Jacobian。$size=n_C\times n_V$
- $f$: Constraint 带来的力。$size=n_C$，有些 DOF 的 Constraint 可能不是 active 的。

MuJoCo 保证 $M$ 是可逆的，根据上述公式，MuJoCo 可以求 foward dynamics 和 inverse dynamics。

forward dynamics 即给出施加的力 $\tau$，求力带来的每个 DOF 的加速度

$$\dot{v} = M^{-1}(\tau + J^Tf-c)$$

inverse dynamics 即给出一个期望达到的加速度，求出达到该加速度需要施加的在每个 DOF 上的外力

$$\tau = M\dot{v} + c - J^Tf$$