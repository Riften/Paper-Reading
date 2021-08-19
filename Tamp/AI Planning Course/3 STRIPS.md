# STRIPS
STRIPS（Stanford Research Institute Problem Solver）包含了 Planning 问题的标准化表示，以及在这些表示上的搜索算法。核心是对 States 和 Actions 的表示，从而能够包含 State 和 Action 本身的内部信息。

## States
定义
- Object Type：物体的类型
- Variable：特定类型的**某个**物体
- Predicates：描述 Variable 之间的关系，在特定状态下，Predicates 可以是 True 或者 False （Hold or Not Hold）。Predicates 之间可能不是独立的。
- ground atoms：一个 ground atom 指的是一个实例化的 Predicate，即 Predicate 以及特定的 Object，而不是变量。
- literal：一个 atom，或者一个取反的 atom
- State：A set of ground atoms （这里的 atom 并不包含 negative atom）
  - Holds $\in$：ground atom $p$ holds in state $s$ $\iff$ $p\in s$
  - Satisfies $\vDash$：给定一个 literals 的集合 $g$，如何 $g$ 中所有 positive literal 都属于 $s$ ，同时所有 negative literal 都不属于 $s$，则称 $s \vDash g$

## Operator
Operator 可以由一个三元组定义 $o = (\text{name}(o), \text{precond}(o), \text{effects}(o))$
- Name: $n(x1, ... x_k)$，$n$ 是标识符，而 $x$ 是变量
- Preconditions & Effects：都是 literals 的集合。需要注意的是 States 中只是 atom，而这里却是 literal。

Action：A ground instance of a planning operator，即一个实例化的 operator。但是在 PDDL 中，Operator 本身被称为 Action。

## Applicability
对于一个 literal 的集合 $L$，我们可以定义两个 atom 的集合
- $L^+$: $L$ 中是 positive 的 atom 的集合
- $L^-$: $L$ 中是 negative 的 atom 的集合

我们称 action $a$ 在 state $s$ 中是 applicable 的当且仅当
- $\text{precond}^+(a) \subseteq s$
- $\text{precond}^-(a) \cap s = \emptyset$ 

Transition Function $\gamma(s,a) = (s-\text{effects}^-(a)) \cup \text{effects}^+(a)$，换句话说就是删掉状态里 negative 的，添上 positive 的

对于特定的 state $s$，以下算法用于寻找某种 operator $op$ 的所有 applicable 的 action $A$
> **function** addApplicables($op$, $precs$, $\sigma$, $s$)
> - $A \leftarrow \emptyset$ # 结果 action 数组