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
  - Satisfies $\vDash$：给定一个 literals 的集合 $g$，如果 $g$ 中所有 positive literal 都属于 $s$ ，同时所有 negative literal 都不属于 $s$，则称 $s \vDash g$

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

一个很关键的问题就是如何决定在某个 state 中可以采取哪些 action。考虑到场景中 object 数量可能很多，实例化到的 action 可能会非常多。对于特定的 state $s$，以下算法用于寻找某种 operator $op$ 的所有 applicable 的 action $A$。
> **function** addApplicables(A, $op$, $precs$, $\sigma$, $s$)
> - **if** $precs^+$.isEmpty() **then**
>   - **for every** $np$ **in** $prec^-$ **do**
>     - **if** $s$.falsifies($\sigma(np)$) **then return**
>   - A.add($\sigma(op)$)
> - **else**
>   - $pp\leftarrow$ $precs^+$.chooseOne()
>   - **for every** $sp$ in $s$ **do**
>     - $\sigma'\leftarrow\sigma$.extend($sp$, $pp$)
>     - **if** $\sigma'$.isValid() **then**
>       - addApplicables(A, $op$, ($precs-pp$), $\sigma'$, $s$)

上述算法中，$A$ 是最终结果的 action 集合，初始为空。$\sigma$ 为对 $op$ 中参数的实例化，初始也为空。算法首先处理 operator 的 positive preconditions （下面 else 部分）。

首先选择一个 positive precondition $pp$，然后遍历 $s$ 的每一个和 $pp$ 有相同 predicate 的 $preposition$（状态本身是一个 preposition 的集合）$sp$。例如 $pp$ 是 `loc1 和 loc2 相邻（这里的 loc1 和 loc2 是参数 variable）`，那么我们选择的 $sp$ 就是当前状态所有对两个位置相邻的命题。

然后我们需要根据选择的 $sp$ 修改 $\sigma$ 的赋值，例如我们选择的 $sp$ 是 `A 和 B 相邻`，那么我们就有 $\sigma'\leftarrow \{loc1 = A, loc2 = B\}$。然而这里的赋值可能与之前的赋值产生冲突，这时候我们会接着遍历下一个 $sp$。直到找到不冲突的赋值。如果遍历完所有 $sp$ 都是冲突的，那么函数就结束了，我们不会继续递归调用 addApplicable。这里的 isValid() 就是在检查赋值是否有冲突是否合法。

如果赋值没有冲突，那么我们就找到了满足 $pp$ 的 $\sigma$，可以继续递归考虑剩下的 $pp$。直到没有剩下的 positive precondition，再最后检查 $s$ 是不是满足 negative precondition。

需要注意的是对 $sp$ 的遍历并不会中断，所以算法会输出所有 applicable action。

## Domains and Problems
Planning 任务的组成
- 初始状态：atom 的集合，或者说 物体和关系的集合。
- Planning Domain：operator 的集合。
- 目标

## Forward State-Space Search
Search Problem 的定义
- initial state $s_i$
- goal test
- path cost $|\pi|$
- successor function $\Gamma(s)$

Successor function 描述的是从一个状态可以到达的后继状态，输入 state，输出 state 的集合。
- $\Gamma(s) = \{\gamma(s,a)|a\in A \text{ and } a \text{ is applicable in } s\}$
- $\Gamma(\{s_1, ... , s_n\}) = \cup_{k\in [1,n]}\Gamma(s_k)$
- $\Gamma^0(\{s_1, ..., s_n\}) = \{s_1,...,s_n\}$
- $\Gamma^m(\{s_1, ... , s_n\}) = \Gamma(\Gamma^{m-1}(\{s_1, ... , s_n\}))$，即表示 m 步可以到达的 state。
- $\Gamma^>(s) = \cup_{k\in[1,\infty]}\Gamma^k(\{s\})$，即从 s 可以到达的所有 state。

Forward Search 算法即每次选择一个 applicable action，直到满足 goal
> **function** fwdSearch($O, s_i, g$) # $O$ 是 operator 集合
> - $state\leftarrow s_i$
> - $plan\leftarrow \langle\rangle$
> - **loop**
>   - **if** $state$.satisfies(g) **then return** $plan$
>   - $applicables \leftarrow$ {ground instances from $O$ applicable in $state$}
>   - **if** $applicables$.isEmpty() **then return** failure
>   - $action\leftarrow applicables$.chooseOne()
>   - $state\leftarrow \gamma(state, action)$
>   - $plan \leftarrow plan\cdot \langle action \rangle$

## Backward State-Space Search
### Relevance
和 applicable 相似的概念，对于一个Task Plan Problem $\mathcal{P}=(\Sigma, s_i, g)$，我们说一个 action $a\in A$ is relevant for $g$ 如果它满足
- $g\cap \text{effects}(a)\neq \emptyset$
- $g^+\cap \text{effects}^-(a) = \emptyset$
- $g^-\cap \text{effects}^+ = \emptyset$

换句话说，$a$ 可以更加接近 $g$。

### Regression Set
目标 $g$ 关于它的一个 Relevant action $a\in A$ 的 Regression Set 定义为
- $\gamma^{-1}(g,a) = (g-\text{effects}(a))\cup \text{precond}(a)$

然后我们可以像定义 Successor Function 那样定义 Regression Function
- $\Gamma^{-1}(g) = \{\gamma^{-1}(g,a)|a\in A \text{ and } a \text{ is relevant for } g\}$
- $\Gamma^{-1}(\{g_1, ... , g_n\}) = \cup_{k\in [1,n]}\Gamma^{-1}(g_k)$
- $\Gamma^{-0}(\{g_1, ..., g_n\}) = \{g_1,...,g_n\}$
- $\Gamma^{-m}(\{g_1, ... , g_n\}) = \Gamma^{-1}(\Gamma^{-(m-1)}(\{g_1, ... , g_n\}))$
- $\Gamma^<(g) = \cup_{k\in[0,\infty]}\Gamma^{-k}(\{g\})$

这样就可以像 Forward Search 一样定义搜索问题，并给出最简单的 Backward Search 算法。
