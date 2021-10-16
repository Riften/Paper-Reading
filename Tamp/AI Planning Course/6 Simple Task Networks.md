# Simple Task Networks (STN)
## Task
- Task Symbols: $T_S = \{t_1, ..., t_n\}$
  - primitive tasks: 即 operator
  - non-primitive tasks
- Task: $t_i(r_1, ..., r_k)$
  - $r$: terms，即 task 所操作的 object
  - ground task: task 也可以被 ground
- Action: 对于 action $a=op(c_1, ..., c_k)$，如果 action 满足下面条件，我们说这个 action accomplishes ground primitive task $t_i(r_1,...,r_k)$ in state s
  - name($a$) = $t_i$, $c_j = r_j$
  - $a$ 在 $s$ 中是 applicable 的
  
## Task Network
- Simple Task network $(U,E)$ 是一个有向无环图
  - 节点是 task $U=\{t_1, ..., t_n\}$
  - 边是 task 之间的顺序 $E=\{t_i\prec t_j,...\}$
  - 如果所有 task 是 primitive 的，我们说整个 STN 是 primitive 的。
  - 如果所有 task 是 ground 的，我们说整个 STN 是 ground 的。
  - totally ordered: 任意两个 task 之间都有边，这时候 task 可以组成一个有序序列
- Plan
  - 如果 STN 是 ordered，ground，primitive 的，那么其相当于一个 action 序列，我们将这个序列定义为 plan $\pi(w) = \langle a_1, ..., a_n\rangle$

## Methods
定义 $m=(\text{name}(m), \text{task}(m), \text{precond}(m), \text{network}(m))$
- name($m$): 唯一的标识符。一个 method n 可以表示成 $n(x_1, ..., x_k)$，$n$ 是标识符，$x$ 是参数，即参与该 method 的物体（变量）。
- task($m$): 是一个 non-primitive task，定义了该 method 所完成的任务。之所以是 non-primitive 的，是因为 primitive task 不需要 method 来完成，只需要 action （operator） 来完成。
- precond($m$): 执行 method 的前提条件
- network($m$): 该 method 的 subtask 构成的 task network，定义了完成该 method 的 task 的方式。

## Decomposition
Task Network 的求解本质上是借助 Method 逐渐将 Task 分解为 Primitive Task 的过程。

Decomposition $\delta(w, t, m, \sigma)$
- $w = (U, E)$ 是一个 STN
- $t$ 是被分解的 task，是 $w$ 中一个没有前继节点的 task。
- $m$ 是和 $t$ 相关的一个 method
- 要想用 $m$ 来分解 $t$，就需要对 $m$ 中的参数赋值为和 $t$ 对应的值，要么 ground 为相同的 object，要么添加变量相同的 constraints，这个赋值表示为 $\sigma$。经过 $\sigma$ 赋值之后的 method network 表示为 $network(m) = (U_m, E_m)$
- 分解过程即将 $t$ 节点替换为一个 network 的过程，具体包含对节点的替换和对边的替换
  - 节点 $t$ 替换为 $\sigma(U_m)$
  - $E$ 中和 $t$ 有关的边都变成和 $\sigma(U_m)$ 中的节点组成的边

## STN Planning
STN Planning Domain $\mathcal{D} = (O, M)$
- O: operators
- M: methods

Total / Partial Order，Method 中的 network 可以是 total order 的，如果 M 中所有 method 的 network 都是 total order 的，我们说整个 Domain 是 total order 的。

对于 Total Order Domain 而言，执行完所有分解过程之后，得到的就会是一个 action sequence。

STN Planning Problem $\mathcal{P} = (s_i, w_i, O, M)$
- $s_i$: Initial State，和 State Space Search 一样，是以个 ground atom 的集合。
- $w_i$: Initial Task Network。Goal 信息也包含在其中。

如果 $w_i$ 和 $\mathcal{D} = (O, M)$ 都是 total order 的，那么称整个 problem 是 total order 的。

## STN Solution
Solution 实际上定义了 STN Search 的终止条件。对于 STN Planning Problem $\mathcal{P} = (s_i, w_i, O, M)$，当 plan $\pi = \langle a_1, ..., a_n\rangle$ 满足下面三个情况之一时，我们称 $\pi$ 是 $\mathcal{P}$ 的 solution

**情况一**：$w_i$ 和 $\pi$ 都为空

**情况二**：
- 有一个没有前继节点的 primitive task $t\in w_i = a_1$，$a_1$ 在 $s_i$ 中可执行
- 去掉 $a_1$ 之后的 plan 是去掉 $t$ 之后的 problem 的 solution，去掉 $t$ 之后的 problem 为 $\mathcal{P}' = (\gamma(s_i, a_i), w_i - \{t\}, O, M)$

**情况三：**
- 有一个没有前继节点的 non-primitive task，可以由 $m\in M$ 分解，且赋值后的 $m$ 在 $s_i$ 可执行
- $\pi$ 是分解后的 problem 的 solution，分解后的 problem 可以表示为 $\mathcal{P}' = (s_i, \delta(w_i, t, m, \sigma), O, M)$

## Ground-TFD Algorithm
Total-Order Forward Decomposition. 简单说就是选择合适的 method，逐渐将所有 non-primitive 的所有 task 都分解成 primitive 的，由于整个 Problem 是 Total-Order 的，所以分解结束时就可以得到一个 solution。

对 Method 的选择过程是 non-deterministic 的，在某个 method fail 的时候需要选择下一个。

对于已经分解为 primitive 的起始 task，根据前面的 solution 判断条件可以直接把这个 task 移除掉。

## Ground-PFD Algorithm