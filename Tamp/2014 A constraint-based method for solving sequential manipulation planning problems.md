# A constraint-based method for solving sequential manipulation planning problems
基于 symbolic search 的 TAMP 方案，最终求解离散 CSP 问题。


## Task-Level Planning
基本上还是按照搜索来进行，只是 operation 中的 precondition 被分为了两部分，symbolic 和 geometric。

- primitive action: 每个 operator 都对应了一个 primitive action，其中包含了 object 的 symbolic name，以及相关的一系列 geometric parameter。primitive action 可以直接由机器人控制程序执行。

constraint variable: operator 中 geometric 部分的 variable，是一个连续 geometric 值的一个取值范围。

plan skeleton: 是 symbolic search 的结果，是一个 primitive task 的序列。其中的 symbolic parameters 有约束关系，而 geometric parameters 则以 constraint variable 的形式存在。constraint variable 之间也有约束关系。

使用 CSP solver 来求解 plan skeleton 中的 constraints 满足情况。

## 具体问题
文中使用简单地 pick and place 作为任务场景，各种概念也根据任务需求进行了具体的建模。

### Constraint Variables
Constraint Variable 被编码为了以下几种
- $H$: robot end effector 的笛卡尔座标系姿态。
- $K$: 整个 robot 的 configuration，即每个轴的角度。
- $G$: Grasps 抓取姿态，物体相对于夹爪的 pose。

### Constraints
Constraint Variable 之间的限制关系，限制关系是互相的，在不同的情形下不同的 variable 可能是需要改变的一方。pick and place 任务中定义的 constraints 包括
- $Grasp(o,p,H,G)$: object $o$ 的姿态为 $p$，夹爪位于 $H$，则相对于 object 的姿态是 $G$，文中把这表示为 $H\circ G = p$
- $Contained(o,p,r)$: $o$ 位于 $p$，则位于 region $r$ 中
- $Disjoint(o_1, p_1, o_2, p_2)$: 位于两个位置的俩物体没有碰撞
- $\exists\text{VP}(H,K,o,G,\mathcal{O})$: 在以姿态 $G$ 抓着 $o$ 的情况下，从当前(home)configuration 到 $(H,K)$ 有一个合法路径。$\mathcal{O}$ 是除了现有避障需求之外额外定义的障碍。