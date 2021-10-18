# Differentiable Physics and Stable Modes for Tool-Use and Manipulation Planning

基本方案
- 使用逻辑表达式表示物理相互关系，并且求解最优结果。在传统 Task Plan 中，逻辑表达式是状态描述和搜索的基础，而这里进一步用逻辑表达式直接描述物理关系。其实相当于 LGP 的 Task Plan 搜索空间更加底层。
- 使用 `mode` 而不是 `action` 作为 plan 的基本单位。

## 相关知识
### First-order logic
一阶逻辑，也就是 PDDL 中使用的 predicate，以及 predicate 组成的 sentence。

### CIO
Contact-Invariant Optimization. 在 [Discovery of Complex Behaviors through Contact-Invariant Optimization](./2012%20Discovery%20of%20Complex%20Behaviors%20through%20Contact-Invariant%20Optimization.md) 中提出的一个自动生成 contact 关系和关节体动作的方案。