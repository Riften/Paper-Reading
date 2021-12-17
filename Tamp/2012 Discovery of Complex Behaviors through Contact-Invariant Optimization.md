# Discovery of Complex Behaviors through Contact-Invariant Optimization
- [Video](https://www.youtube.com/watch?v=mhr_jtQrhVA)

用 CIO 来生成复杂行为（动作），可以是人的动作，也可以是任何其他可动形体的动作。

文章最终解决的问题是动作的自动合成（automated synthesis），期望在不提供关键帧、轨迹、运动细节等知识的情况下，自动合成所有人体可以执行的行为动作。之所以用 behavior 是因为这里的行为还包含了与环境或者其他形体的接触交互。

Contact-Invariant：这里的 Contact 指的是关节体之间的接触，例如人走路的时候和地面接触，拿取物体的时候和物体接触。Invariant 则指的是，交互的过程可以分为多个 Contact 关系维持不变的阶段 (phase)。传统的基于运动学建模的运动生成方法，这种 contact 是定义在模型中的，而本文希望提供一般化的发掘合适的 contact sets 的方式。注意，不变的是 contact 参与情况，而不是 contact force 受力情况，受力情况是变化很大的。

CIO 的一个核心想法就是将 Contact 关系编码成 **连续** 变量，来和 Movement Trajectory 一起优化求解。例如变量 $c_i$ 代表一个 contact 关系，然后定义 $c_i$ 值越大，代表该 contact 对当前 phase 而言越重要，越需要保证是 active 的。

Contact 变量还与 dynamics 直接相关，只有当 $c_i$ 足够大时，才允许在该 contact 上附加 contact force。同时，通过将 contact 的选择纳入优化过程，也不需要根据碰撞关系来复杂的计算 contact。

另外，在通常的基于优化的运动生成策略中，优化的空间是 joint configuration，最终的 end-effector 是通过 FK 算出来的。而在本文中，优化空间直接是 end-effector，joint configuration 是通过 IK 算出来的。

## 具体方案
- $s$: Solution，可以有不同的形式，例如一个 Configuration 序列，或者一个 Configuration 的 splines。本文中 Configuration 仅仅包含 end effector 的 pose，使用 spline 作为 solution 形式

### Contact Violation Vector & Contact Invariant Cost
$e_{i,t}$ 代表 end-effector $i$ 在时间 $t$ 和环境的 Contact Violation Vector
- 前三维代表和环境中最近点的距离
- 最后一维代表 end effector 和 最近点 的 surface normal 的夹角。

定义 auxiliary variable $c_{i,\phi(t)}$ 代表 contact 变量，该变量越大，意味着其对应的 contact 应当存在。$i$ 和 end effector 的 id 一致，换言之对每个 end effector 有对应的 contact 变量。$\phi(t)$ 是 时间$t$ 所属的 phrase。每个 phrase 中，一个 contact 要么一直存在，要么一直不存在，所以用 $\phi(t)$ 区分不同 phrase 的 contact 变量。

Contact Invariant Cost:

$$L_{CI}(s) = \sum_{t}c_{i,\phi(t)}(s)\large(\normalsize\lVert e_{i,t}(s)\rVert^2+\lVert\dot{e}_{i,t}(s)\rVert^2\large)$$

在优化问题中，这里的 $e$ 和 $c$ 都是优化的变量，$e$ 是 end effector $i$ 的 pose $q_{i,t}(s)$ 的函数。如果没有其他限制，最小化 $L_{CI}$ 的结果是所有变量都为 0。

当优化问题发现想要达成某个条件，需要施加某个 contact force 的时候，就需要相应的 $c_i$ 足够大，这时候最小化 $L_{CI}$ 的结果就是 $e=0$，也就是移动到 contact 发生位置。

## Implementation
参考一份CIO的 Matlab 实现：[Contact-Invariant-Optimization-Project](https://github.com/robbierolin/Contact-Invariant-Optimization-Project)

总体来说实现的比较狗屎

- `getConstraints`: 获取全局参数，包括
  - `K=10`: phrase 数量
  - `PhraseLength=0.5`: 每个 phrase 的时长（0.5 秒）
  - `deltaT = 0.1`: 每个 time step 的时长
  - `T`: 总 time step 数量，T=PhraseLength*K/deltaT
  - `N=4`: end effector 数量
- `getS0`，得到 10K 维向量 `x` 和 4K 维向量 `c`，称为 `s0`
  - 获取 Start 和 Goal 姿态，包括
    - 躯干位置和旋转
    - 四个 endeffector 的位置和旋转
    - configuration 被整合为一个 10 维向量，初始为 `x_1`，goal 为 `x_K`
    - 对 `[x_1, x_K]` 进行插值，得到所有 K 个 configuration
    - 将所有 configuration 拼接成一个 10K 的向量 `x`
    - 初始化 `x'`，由于 `x_i` 是直接插值得到，所以这里可以直接 $x_i'=(x_K-x_1)/TotalTime$，把`x_i'`重复K次拼起来就是 `x'`
  - 获取 contact 变量初始值
    - 每个 time step 有四个 contact 变量，对应四个 endeffector 和地面的 contact。
    - 定义好初始和goal的contact值，希望发生contact的初始化为100，不希望发生contact的初始化为0
    - 为剩下的 `K-2` 个状态创建对应的 contact 变量并初始化为0
    - 拼接在一起得到 4K 维的 `c`
- 创建 solver 并进行配置，solver 使用的是 matlab 自带的 `fminunc`，配置使用的是函数 `optimoptions`
- 第一阶段优化 `OptimizationPhase1`: 总体上是在不考虑 contact 的情况下，保持物理上的运动平滑，计算一条 start 到 goal 的路径
  - `L_Physics`：目的是让 contact force + control force 可以提供足够的加速度让模型正确运动。
    > The other is specific to the simplified model of multi-body dynam- ics described below – which can be replaced with a full physics model without modifying the rest of the method.
    - 获取 configuration space `pose`，由函数 `q` 得到。`pose` 是一个 (Tx30) 矩阵，每一行是一个 time step 的每个部位的位置和旋转，一共5个部位。
      - 通过 `getBodyPosition` 把向量从 `x_k` 中重新获取出来，躯干+4个 end effector，每个部位一个位置一个旋转，总共10个 (3xK) 维矩阵。
      - 对获取到的 10 个矩阵创建样条曲线做平滑
      - 从样条曲线中采样，得到 (Tx30) 维矩阵 `ret`
    - 计算 `pose'` 和 `pose''`，都是用差分计算的。计算公式如下：
    $$X' = \frac{1}{\Delta T}\left[\begin{array}{c}
        1 & 0 & 0 & ... & 0 & 0 \\
        -1 & 1  & 0 & ... & 0 & 0\\
        0 & -1 & 1 & ... & 0 &0\\
        ...\\
        0 & 0 & 0 & ... & 1 & 0\\
        0 & 0 & 0 & ... & -1 & 1
    \end{array}\right] X$$
    - 得到 dynamics 参数，包括
      - Inertia Matrix `M(DxD)`，`D`为自由度 DOF，4个eef加上一个躯干，每个部位6个自由度，$D=(4+1)*6 = 30$。然而在实际计算中，`M` **被简化成了一个 30 维向量**。`M = [4 4 4 2 2 2 2 2 2 1 1 1 1 1 1 4 4 4 2 2 2 2 2 2 1 1 1 1 1 1]`，这里相当于定义躯干、手臂、腿的质量分别是 `4,2,1`，整个机器人质量为 10
      - Jacobian `J`，**这里并没有得到真正的 Jacobian，只有一个不明所以的J**
    - 遍历每一个 time step
      - 计算 $\tau$，物理意义是达到当前运动状态所需要施加的外力。
        - 计算与地面之间的 contact force，计算方法是，对于五个部位，如果和地面的距离 <0.1，则计算 contact force = $10*9.8-q''*M_i$，这里的 $M_i$ 是与地面接触的部位在 Z 方向上平移对应的 `M` 分量。重力加速度之所以要 *10 是因为定义了机器人的`M`是 4+2+2+1+1=10
        - 计算完每个部位和地面的 contact force 之后，还需要将每个 contact force 除以和地面接触的部位数量，相当于均摊 contact force
        - 计算 applied forces = $q''\cdot M$，相当于加速度每个分量乘上一个质量
        - $\tau=$applied_forces + contact_forces，本意是 $\tau=M\ddot{q} + C\dot{q}+g$，但是这里算得很抽象。
      - 计算 $f$, $u$
        - 通过求解一个子优化问题，
        $$\begin{aligned}
          f,u = \argmin_{\tilde{f}, \tilde{u}}&\lVert J^T\tilde{f} + B\tilde{u} - \tau \rVert^2 + \tilde{f}^TW\tilde{f} + \tilde{u}^TR\tilde{u}\\
          \text{subject to  }& A\tilde{f} \leq b
        \end{aligned}$$
        - 这里的限制条件是简化的 friction cone，目的是保证 contact 的方向和大小位于摩擦椎内。但是实际实现中非常简化。
        - 计算 `W`
        - 计算 $cost = \lVert J^Tf+Bu-\tau\rVert^2$，但是实际实现中这个 cost 只是计算出来了，似乎没有参与优化。换句话说以阶段优化只优化了后面的 `L_Task`
  - `L_Task`
- 给一阶段优化之后的 `s` 加上噪声 `s(i) = s(i) + normrnd(0,0.1);`
- `OptimizationPhase2`，同时考虑 `L_CI`，`L_Task`，`L_Physics`，`L_Hint`
- `OptimizationPhase3`，不再考虑 `L_Hint`


> 关于 fminunc 函数

## 使用物理引擎实现 CIO 的核心问题
- L_Physics 的计算，$\tau$ 可以通过 inverse dynamics 直接得到。但是对于优化问题来说，需要得到的是 **$\tau$ 和当前能得到的最接近和外力之间的差值**。