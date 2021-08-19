# The Fast Downward Planning System
整合了启发式搜索算法的传统 Planning System，搜索方式主要是前向搜索。

Fast Downward 使用的搜索方式本质上还是启发式搜索 Heuristic Search，只不过使用的 Evaluation Function 是特殊的 Causal Graph Heuristic。

## 核心概念
### Causal Graph
> The causal graph of a planning task contains a vertex for each state variable and arcs from variables that occur in preconditions to variables that occur in effects of the same operator.

Planning 问题中的一些状态变量之间可能存在依赖关系，例如在文中实例 “邮递问题” 中，邮包的状态能否从 “位于城市 A” 转变为 “在车辆 c_1” 里，有 precondition “车辆 c_1 在 A”。所以这里的改变邮包状态的 load operator 就包含了邮包状态和车辆位置之间的依赖关系。

但是实际的 Planning 问题中，Casual Graph 可能不是无环的，这就给在 Casual Graph 中进行搜索带来了困难。在解决这类 Planning 问题的时候，会忽略一部分因果依赖。

### Multi=Valud encoding
Planning Task 的一般描述中，缺少变量本身的层级关系，例如 “描述邮包位置” 和 “描述车辆位置” 的变量，使得难以分析出清晰的 causal graph。

所以文章的一大目标就是对于用 PDDL 描述的一般化的 Planning Task，可以将原本的命题描述方式转变成基于 Multi-Value 的问题描述。

### Subproblem
单个状态变量以及其在 causal graph 中的前节点相关的问题可以构成一个 subproblem，文中的 Planner 将原 Problem 划分成 Subproblem 来解决。