# General Framework
## 基本运动方程

$$M\dot{v}+c = \tau + J^Tf$$

- $M$: 惯性矩阵，$n_V$ 为 DOFs 数量，则 $M$ 尺寸为 $n_V\times n_V$
- $\dot{v}$: 所有自由度速度的一阶导数，也就是加速度， $size=n_V$
- $c$: 一些外在的偏向力（bias force），例如重力、地转偏向力、整个座标系在旋转时的离心力。$size=n_V$
- $\tau$：施加的力。$size=n_V$。系统被动承受的力也在这一部分，例如流体或弹簧系统带来的阻力。
- $J$: Constraint Jacobian。$size=n_C\times n_V$
- $f$: Constraint 带来的力，可以理解为是与环境的 contact 带来的力。$size=n_C$，有些 DOF 的 Constraint 可能不是 active 的。

MuJoCo 保证 $M$ 是可逆的，根据上述公式，MuJoCo 可以求 foward dynamics 和 inverse dynamics。

forward dynamics 即给出施加的力 $\tau$，求力带来的每个 DOF 的加速度

$$\dot{v} = M^{-1}(\tau + J^Tf-c)$$

inverse dynamics 即给出一个期望达到的加速度，求出达到该加速度需要施加的在每个 DOF 上的外力

$$\tau = M\dot{v} + c - J^Tf$$

## 关于 $c$
joint-space bias force: 定义是想要让 joint space 处于 0 加速度需要施加的力。

由 configuration 和速度决定的力 $c(q,v)$，一般包括重力、地转偏向力、整个系统的离心力 的负值。

在 mujoco 中，提供了 `mjData.qfrac_bias` （nv x 1）存储 $c$ 在每个 DOF 上的分量。

## Pipeline
模拟器的一个基本假设是速度连续，而加速度不连续。模拟过程中，速度和位置都是逐步更新的一个量，但是加速度（和力）都是每一步单独计算的。

### Forward Dynamics
输入加速度，更新位置速度，输入和计算力，输出加速度

```cpp
void mj_step1(const mjModel* m, mjData* d)
{
    mj_checkPos(m, d);
    mj_checkVel(m, d);
    mj_fwdPosition(m, d);
    mj_sensorPos(m, d);
    mj_energyPos(m, d);
    mj_fwdVelocity(m, d);
    mj_sensorVel(m, d);
    mj_energyVel(m, d);

    // if we had a callback we would be using mj_step, but call it anyway
    if( mjcb_control )
        mjcb_control(m, d);
}
```
step1 相当于根据上一次模拟的所有加速度参数，计算位置等信息，以及完成基于位置和速度的各个传感器、驱动器参数，以及由位置和速度决定的力大小
- 检查 pos 和 vel 的合法性，如果非法则系统状态会被重置。
- forward kinematics，所有物体的位置和角度计算。
- 计算 body inertia 和 joint axe
- 计算驱动器状态，例如有肌肉模拟的话会计算肌肉被拉伸的状态
- 计算 Joint-Space Inertia Matrix
- 计算 Joint-Space Inertia Matrix 的稀疏化表示参数。
- collision detection，构建 active 的 contact 列表
- 计算 constraint jacobian 和 residuals
- 计算 constraint 求解器需要的各项参数
- 计算那些依赖于位置和势能场的传感器参数
- 计算驱动器速度
- 计算各个 body 和 joint axe 的速度
- 计算被动阻力 passive force，例如弹性阻尼和流体阻力。
- 计算依赖于速度和动能的传感器参数
- 如果存在外力输入，则调用响应回调函数输入外力

```
void mj_step2(const mjModel* m, mjData* d)
{
    mj_fwdActuation(m, d);
    mj_fwdAcceleration(m, d);
    mj_fwdConstraint(m, d);
    mj_sensorAcc(m, d);
    mj_checkAcc(m, d);

    // compare forward and inverse solutions if enabled
    if( mjENABLED(mjENBL_FWDINV) )
        mj_compareFwdInv(m, d);

    // integrate with Euler; ignore integrator option
    mj_Euler(m, d);
}
```
计算加速度
- 计算约束加速度
- 计算重力、离心力、科里奥利力
- 计算驱动器致动力
- 计算 joint 在除了 constraint force 作用下的加速度
- 计算 constraint force 作用下的加速度，至此完成加速度计算，写入 `mjData.qacc`
- 计算基于加速度和力的传感器参数
- 检查加速度合法性
- 如果 `mjENDB_FWDINV`，则检查 fowrard 和 inverse dynamics 之间的差异，验证模拟精确度。

### Inverse Dynamics
给出加速度信息，计算需要施加的力。