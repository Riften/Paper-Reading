# Shakey The Robot
是最早的 TAMP 研究之一，用 Stanford 的问题求解系统 Stanford Research Institute Problem Solver (STRIPS) 求解 Task Plan 问题，用 Sequencing First 的策略分阶段解决 Task Plan 和 Motion Plan。

给出了 Task Plan 的一般形式和需要考虑的问题，但是这里讨论的 Task Plan 还是基于高度抽象化的 Action，不包含 Geometry 信息，只是简单的对 State Space 中的搜索方式进行了求解。

## 术语
- Operator：包含参数、precondition、effects 的一个操作。
- 经典 Planning 问题的定义 $(I, G, A)$
  - $I$: Initial State
  - $S$: State space, 总的状态空间。
  - $G \subseteq S$: 目标状态
  - $A$: 一系列可以执行的 actions
- 问题的解的形式为一系列的 action
- 问题的描述形式通常采用特定的语言，例如这里使用的求解系统 STRIPS 有自己的语言。后续发展起来的 PDDL 也是这个目的。
- 用集合表示 Planning 问题
  - Transition System $\mathcal{T} = <S, L, T, s_0, S_*>$
    - $S$: 有限的状态集合
    - $L$: transition label
    - $T\subseteq S\times T\times S$: 状态之间的转换关系
    - $s_0$: 初始状态
    - $S_*\subseteq S$: 目标状态
  - Planning 问题也可以描述为一个 Transition System
    - 首先理解 proposition symbols 命题符号，可以理解为用命题表示的变量，在 Task Plan 的场景中这种符号例如 `c在表面s上`。proposition symbols 的值为 True / False。
    - $L = \{ p_1, p_2, ... , p_n \}$ 是 proposition symbols 的集合，这时 $L$ 就可以看作一种 Language。
    - $S \subseteq 2^L$ 是状态集合，之所以是子集是因为有些 proposition 可能有关联性。
    - action 可以定义为 $a = (pre(a), effects^+(a), effects^-(a))$，其中的 $pre(a)$, $effects^+(a)$, $effects^-(a)$ 是 $L$ 的三个子集，也就是说是三个 proposition symbol 的集合。这里的 effect 之所以分为两部分，是因为一部分是 action 执行后为真的 proposition symbol，一部分是为伪的。
    - 如果 $pre(a) \in S$，我们说 $a$ 是 applicable 可执行的。
    - transtion function 可以表述为 $\gamma(s, a) = (S-effects^-(a))\cup effects^+(a)$
    - 由 $\Sigma=(S, A, \gamma)$ 组成的 restricted transition system 就是 Planning 问题的另一种表述。

本文中使用的语言 $L$ 为 *First-order language with finite many predicate and constant symbols.*

## 算法
### Forward Search
Search(D, I, g) # $D=(S, A, \gamma)$
- $s\leftarrow I$ # 初始化 $s$ 为初始状态
- $\pi \leftarrow \emptyset$ # $\pi$ 是最终找到的 solution
- loop
  - if($s$ satisfies $g$)
    - return $\pi$
  - $applicable \leftarrow \{a | a \text{ is a ground action of } D \text{ and } precond(a) \text{ is } true \text{ in } s\}$ # 找到当前状态下可以采取的所有 action
  - if($applicable = \emptyset$)
    - return failure
  - choose an action $a\in applicable$
  - $s\leftarrow \gamma(s, a)$ # 更新当前状态
  - $\pi \leftarrow \pi.a$ # 更新结果
  
这里其实是对图 $D$ 进行了一个前向遍历。这个算法当然是不可行的，因为 Task Plan 问题中的 $D$ 是有环的。

### Backward Search
后向遍历。