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