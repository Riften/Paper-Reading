# Differentiable Physics and Stable Modes for Tool-Use and Manipulation Planning
**LGP** 的论文。

LGP 和核心突破在于，当给出一个 Plan Skeleton 的时候，LGP 可以同步优化整个 Skeleton 中所有参数。

基本方案
- 使用逻辑表达式表示物理相互关系，并且求解最优结果。在传统 Task Plan 中，逻辑表达式是状态描述和搜索的基础，而这里进一步用逻辑表达式直接描述物理关系。其实相当于 LGP 的 Task Plan 搜索空间更加底层。
- 使用 `mode` 而不是 `action` 作为 plan 的基本单位。

## 相关知识
### First-order logic
一阶逻辑，也就是 PDDL 中使用的 predicate，以及 predicate 组成的 sentence。

### Differentiable Physics
一般的物理模拟是一个 Forward 的过程，给出物理状态，计算碰撞重力等，模拟状态变化过程。而 Differentiable Physics Engine 可以双向模拟物理过程，即给出希望的结果，模拟出应当设定的参数。

### CIO
Contact-Invariant Optimization. 在 [Discovery of Complex Behaviors through Contact-Invariant Optimization](./2012%20Discovery%20of%20Complex%20Behaviors%20through%20Contact-Invariant%20Optimization.md) 中提出的一个自动生成 contact 关系和关节体动作的方案。

## 问题定义
LGP 的测试场景是有物理交互参与的问题，例如击球这种需要考虑物理交互方式的问题。

LGP 解决的是以个优化问题，那么就包含两个部分，优化目标，限制条件。

## 核心概念
### Mode

### New Predicates
通过定义包含物理信息的 predicate 来向 path optimization 添加约束。这些 predicate 仅仅出现在 action 的 effect 字段中。

这些 predicate 并不仅仅有搜索上的作用，或者说因为它只出现在 effect 中所以这部分 predicate 并不会影响搜索过程。但是这部分 predicate 会直接影响到 object 之间，或者 agent 与 object 之间的运动学关系。可以说这些新的 predicate 是对 mode 的限制。

## 实现
使用了作者之前发布的求解器 [Multi-Bound Tree Search (MBTS)](./2017%20Multi-Bound%20Tree%20Search%20for%20Logic-Geometric%20Programming%20in%20Cooperative%20Manipulation%20Domains.md).
