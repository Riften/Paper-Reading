# Sampling-Based Methods for Motion Planning with Constraints
约束采样 Motion Plan 的一篇综述。

基于约束采样 Constrained Sampling 的 Motion Plan 方法总体上有两个操作
- 采样得到能够满足约束的 configuration
- 生成连续的 motion

Sampling-Based 的方案基本原理是，随机生成 robot configuration （也就是采样），然后在有限时间内连接采样得到一条满足所有 constraints 的 motion。这样做可以避免对 Free Space $Q_{free}$ 本身的计算。

## 数学表述
- Configuration Space $Q$，代表机器人的所有配置（构形），是一个维度为机器人自由度 $n$ 的空间。
- Free Space $Q_{free} \subseteq Q$，不与环境或者自身碰撞的 configuration
- Motion Planning Problem：给出起始配置 $q_{start} \in Q_{free}$ 和目标配置 $Q_{goal} \subseteq Q_{free}$，找到一条连续的路径 $\tau :[0,1] \rightarrow Q_{free}$，$q_{start} = \tau(0)$，$\tau(1) \in Q_{goal}$。
- Constraint Function $F: Q\rightarrow \mathbb{R}^k$，当 $F(q) = \mathbf{0}$ 时候认为 $q$ 满足约束。这里的 $k$ 可以理解是 constraint 限制的维度数量。
- Constrained configuration space $X=\{q\in Q | F(q) = \mathbb{0}\}$，由约束函数定义的 $m$ 维构形空间。
- Motion Planning Problem with Constraints: 需要找到的路径需要属于 $\tau: [0, 1]\rightarrow X_{free}$，$X_{free} = X \cap Q_{free}$


## Sampling-Based Motion Planning
- Probabilistic Roadmap Method (PRM): 首先通过不断地 sample 逐渐扩展 roadmap，每增加一个节点都会相应的生成 collision free motion 作为边，直到 start 和 goal 在同一个 component 里面，再用 A* 一类的算法求出最终的 path 作为结果。
- Rapidly-exploring Random Tree (RRT): 类似的方法构建一个 Tree。

基于采样的 Motion Planner 通常需要一些共通的组成部分，包括
- Samplers：采样器，最终目标都是采样到 $q\in Q_{free}$。
- Metrics & nearest-neighbor data structires: 用来得到 configuration 的 neighbor。
- Local planner：计算临近 configuration 之间的 path，最简单的方式是直接用插值。
- Coverage eitimates

## With Constraints