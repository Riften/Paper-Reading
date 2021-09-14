# Integrated Task and Motion Planning
Tamp的综合survey，对Tamp牵扯到的问题进行了分类，并整理了解决这些问题的常见方法。

## Motion Planning 问题
让机器人从一个状态转变到另一个状态，同时避免与其他物体碰撞。本质上是在机器人的configuration space的一个搜索问题。

**数学表述**
- $\mathbb{R^d}$：机器人的configuration space可以看作是一个d维的空间（对于多轴机器人来说可以看作是每个轴一个自由度组成的空间）
- $\mathcal{Q}$：机器人合法的configuration space是该d维空间的一个子集$\mathcal{Q}\subset \mathbb{R}^d$.
- 对于实际问题来说，还会有具体的对 $\mathcal{Q}$ 的constraint，这种constraint可以表述为从$\mathcal{Q}$中的值到True/False的一个映射，$F:\mathcal{Q}\rightarrow \{0, 1\}$
- motion plan 的初始状态是空间 $\mathcal{Q}$ 中的一个点 $q_0\in \mathcal{Q}$.
- motion plan 的目标状态可能不唯一，是 $\mathcal{Q}$ 的一个子集，$Q_*\subseteq\mathcal{Q}$
- plan过程中需要保持状态满足 constraint $F$，或者说状态始终是 feasible configuration，这是$\mathcal{Q}$中满足$F$的值组成的子集，$Q_F = \{q \in \mathcal{Q} | F(q) = 1\}$.
- motion plan 的最终目标是找到一个连续的路径 $\tau : [0,1] \rightarrow \mathcal{Q}$，该路径满足：
  - 起点为$q_0$，$\tau(0) = q_0$
  - 重点是一个目标状态， $\tau(1) \in Q_*$
  - 过程中始终满足$F$，$\forall \lambda \in [0,1], \tau(\lambda) \in Q_F$

最简单的motion plan问题，把机器人从平面位置A移动到位置B同时避免与环境中物体碰撞。该问题中，$\mathcal{Q}$ 是房间平面，限制 $F$ 是不与任何物体碰撞，初始状态是起点 $q_A$，目标状态是终点 $Q_* = {q_B}$，$Q_F$ 是房间中所有没物体的地方，最终的解 $\tau$ 是从A到B的一条路径。

## Multi-Model Motion Planning (MMMP)
除了motion plan，还包括对环境状态的影响。

**环境建模**：对环境几何状态的一种建模方式是 Kinematic Graphs。

**Constrained Motion Planning**

对于MMMP问题来说，configuration space 不单单包含了 robot 的自由度本身，还包含了和周围环境组成的整个系统的状态，我们将这个 configuration space 表述为 $\mathcal{W}$，将满足 constraint 的 configuration space 表述为 $\mathcal{W}_F$。

MMMP问题的一个复杂的地方在于 $\mathcal{W}_F$ 通常比 $\mathcal{W}$ 有更低的维度。例如一个抓取物体移动的问题，原本的 $\mathcal{W}$ 即包含了机器人的自由度，也包含了物体的自由度。但是抓取物体的过程中，物体实际上没有自由度。这就使得无法直接使用基于采样的方式求解。

作为替代的方式是 constrained motion planner，可以理解为直接在 $\mathcal{W}_F$ 上进行plan，而不是将满足 $F$ 看作一个plan的目标。

**数学表述**
- 模式参数$\sigma$，表示在 $\mathcal{W}$ 基础上的限制信息，例如物体和 graper 的 attach 关系，robot 拉动物体的姿势，之类。在 plan 过程中 $\sigma$ 是一个定值。
- 整个系统的状态不止包含了$w\in \mathcal{W}$，也包含了模式参数，$s=\langle w, \sigma \rangle$。还有另一种状态表示形式是$s\langle q, K\rangle$，其中 $q \in \mathcal{Q}$ 是机器人的 configuration ，$K$ 是 Kinematic Graph。
- 一个 MMMP 问题不止有一种模式参数，他可以有一个 mode families 集合 $\{ \Sigma_1, ..., \Sigma_m \}$。每一个 mode families 都有一个参数 $\theta$，一个特定的模式可以表示为 $\sigma = \Sigma(\theta)$
- 限制条件 $F_\sigma$ 是一个加在整个系统上的全局 configuration。
- 系统的模式可以切换，mode switch 意味着系统的模式从 $\sigma$ 切换到 $\sigma'$，这通常伴随着系统的 Kinematic Tree 结构的改变。
- MMMP 问题的目标为 $\mathcal{W}_*$，起始状态为 $\langle w_0, \sigma_0 \rangle$
- MMMP 问题的解可以表述为 $[\sigma_0, \tau_0, \sigma_1, \tau_1, ... , \sigma_k, \tau_k]$，其中 $\tau_i$ 是满足 $F_{\sigma_i}$ 的一条路径，且有 $\tau_i(0) = \tau_{i-1}(1)$ 来保证路径的连续性。最终的 $\tau_k(1) \in \mathcal{W}_*$

例如一个抓取物体到特定位置的问题中，有两类 mode families，没有抓任何物体的 transit mode，和抓了某一个物体的 transfer mode。对于 transit 来说，模式参数 $\theta$ 为每个可移动物体的姿态。对于 transfer mode 来说，模式参数 $\theta$ 除了可移动物体姿态，还包括了被抓取物体的抓取姿态。

## Task Planning
即生成任务的过程。

**数学表述**
- 状态空间 $\mathcal{S}$，系统所有状态的集合。
- transitions $\mathcal{T} \subseteq \mathcal{S} \times \mathcal{S}$，代表状态之间的转换
- 初始状态 $s_0 \in \mathcal{S}$，目标状态 $S_* \subseteq \mathcal{S}$
- 参数化表示。使用参数化表示的很大一个原因是，状态空间通常非常大，参数化可以让我们能够处理庞大的参数空间
  - 状态可以由一系列参数描述 $\mathcal{V} = \{1, ..., m\}$，每一个变量 $v$ 都有一个有限的值域 $\mathcal{X}_v$
  - 状态空间可以看作每个变量值域的笛卡尔积 $\mathcal{S} = \mathcal{X}_1, ..., \mathcal{X}_m$
  - 一个特定的状态就可以由一个变量值 $x_v \in \mathcal{X}_v$ 的组合表示
- transitions 的参数化
  - 一个 transition 可以表示为一个 action，action 可以由其产生的影响和发生的条件定义。
    - 对每个变量 $v$ 的值的影响 $\textbf{eff: } \{v_1 \leftarrow c_1, ..., v_k \leftarrow c_k\}$
    - action 发生的条件 preconditions $\textbf{pre: } \{ x_{v_1} = c_1, ..., x_{v_k} = c_k \}$

## Task and Motion Plan
### 解的形式
TAMP 的解首先需要一个 plan skeleton，它包含了 action template 和 参数数量等信息，相当于没有赋具体值的解。

### Hybrid Constraint Satisfaction
得到 plan skeleton 之后需要找到每个 variable 的可行的赋值，这本质上是一个 Hybrid Constraint Satisfaction 问题。

> Hybrid Constraint Satisfaction
> - [Wikipedia: Constraint Satisfaction](https://en.wikipedia.org/wiki/Constraint_satisfaction)
> - [Wikipedia: Hybrid Constraint Satisfaction](https://en.wikipedia.org/wiki/Hybrid_algorithm_(constraint_satisfaction))
> - 《人工智能 —— 一种现代的方法》 第六章
> 
> 约束满足问题 (Constraint Satisfaction, CSP) 是寻找一组变量必须满足的限制条件的解的过程，最终的解组成的变量取值集合叫做 feasible region。可以看出来，文章中对 Motion Plan 等问题的描述方式都是按照约束满足问题来的。
> 
> 约束满足问题的一般化定义为：
> - 变量集合 $X=\{X_1, X_2, ... X_n\}$
> - 变量值域 $D=\{D_1, D_2, ... D_n\}$
> - 描述变量取值的约束集合 $C$

解决 H-CSP 问题的一个简单的办法就是转换成数学规划问题（Mathematical Programming），通过求解限制条件把所有参数的值一并解出来。另外，由于 TAMP 的参数中通常有离散值，所以数学规划的形式通常是混合整数规划（Mixed-Integer Programming, MIP）。但是对于 TAMP 任务中的 H-CSP 问题来说，数学规划的计算复杂度过高了。

另一个求解 H-CSP 问题的思路是不一次性满足所有约束，而是通过构建特定的采样器（sampler）来一次满足部分约束。而为了避免约束造成的维度下降带来的采样困难，在解决 TAMP H-CSP 的时候一般使用条件采样器（Conditional Samplers），并且将多个条件采样器组成采样网络（Sampling Network）使用。

### Combining action sequence and parameter search
有了 Task Plan 和 H-CSP 问题的求解方式之后，需要将这两部分结合起来，从而求解完整的 TAMP 问题。总体上有三种结合策略
- Sequence Before Satisfy：首先得到完整的 plan skeletons，然后寻找满足所有 constraint 的参数值。
- Satisfy Before Sequence：先采样得到参数值，然后尝试组装成完整的 sequence
- Interleaved：一步步将操作添加到 plan 上，递增的满足每一步的 constraints。

各种 TAMP 算法很大程度上就是在这三种策略基础上进行改进。

**Sequence Before Satisfy**：早期 TAMP 算法采用的策略。也就是先得到整体的 Plan Skeleton，然后解决 Skeleton 中的 H-CSP 问题。

## Communication Between Subproblems
TAMP 求解过程中可能牵扯到多个 H-CSP 问题，而这些问题可能会有一些共同的 Constraint。这里的基本想法就是，在求解 H-CSP 问题的时候，记下来一些 Constraint 的信息，然后在求解别的 H-CSP 问题的时候复用这些信息。