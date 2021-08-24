# The Fast Downward Planning System
整合了启发式搜索算法的传统 Planning System，搜索方式主要是前向搜索。

Fast Downward 使用的搜索方式本质上还是启发式搜索 Heuristic Search，只不过使用的 Evaluation Function 是特殊的 Causal Graph Heuristic，从而能够以 Causal Graph 为层级关系，“自上而下 Downward” 解决 Casual Graph 中的 subproblem。

## 核心概念
### Causal Graph
> The causal graph of a planning task contains a vertex for each state variable and arcs from variables that occur in preconditions to variables that occur in effects of the same operator.

Planning 问题中的一些状态变量之间可能存在依赖关系，例如在文中实例 “邮递问题” 中，邮包的状态能否从 “位于城市 A” 转变为 “在车辆 c_1” 里，有 precondition “车辆 c_1 在 A”。所以这里的改变邮包状态的 load operator 就包含了邮包状态和车辆位置之间的依赖关系。

但是实际的 Planning 问题中，Casual Graph 可能不是无环的，这就给在 Casual Graph 中进行搜索带来了困难。在解决这类 Planning 问题的时候，会忽略一部分因果依赖。

### Multi-Value encoding
Planning Task 的一般描述中，缺少变量本身的层级关系，例如 “描述邮包位置” 和 “描述车辆位置” 的变量，使得难以分析出清晰的 causal graph。

所以文章的一大目标就是对于用 PDDL 描述的一般化的 Planning Task，可以将原本的命题描述方式转变成基于 Multi-Value 的问题描述。

文章最终采用的 Task 描述格式是 **State-variable planning under structual restrictions: algorithms and complexity** 里面提出来的 `SAS+`，可以通过在执行 `fast-downward.py` 的时候加上参数 `--keep-sas-file` 来保留 sas 文件，这样会在当前目录存在一个 `output.sas` 文件。

## 总体求解过程
- translation: 把 PDDL 翻译成 SAS 的过程
- knowledge compilation: 生成 search 所需要的各种结构体，包括
  - Domain transition graphs: 描述 state variable 的改变条件
  - Causal graph: 描述 state variable 之间的层级依赖关系
  - successor generator: 可以高效的决定给出的一个 state 可以执行那些 applicable operator
  - axiom evaluator: 计算 variable 的值
- search: 文章实现了三种 search 算法
  - greedy best-first search with causal graph heuristic
  - multi-heuristic best-first search
  - focused iterative-broadening search

### Subproblem
单个状态变量以及其在 causal graph 中的前节点相关的问题可以构成一个 subproblem，文中的 Planner 将原 Problem 划分成 Subproblem 来解决。

## Implementation
Fast Downward 的实现总体上包含两部分，用 python 实现的 translator 和 借助 Line Programming Solver 用 C++ 实现的 searcher。而运行的方式是直接通过可执行程序，虽然直接执行的是 python 脚本，但是这里的 python 脚本只是充当了一个 arguments parser （命令行 parse 工具你不用，各种 binding 工具你不用，写了一堆怎么 find executable 的脚本，真 JB 丑）。

**Source Tree**
- src
  - translate: translator 实现
    - pddl: 用 python 实现的 programmable pddl，包含 pddl 里面不同组成部分的实现类，可以通过脚本进行修改，且可以 dump 到文本文件。
    - pddl_parser: 将文本文件 parse 到 programmable pddl。对外接口是 `pddl_file.py:open()`，输入 domain 和 problem pddl file 的路径，返回一个 `pddl.Task`
    - *.py：multi-value encoding 的实现。
      - 对外接口是 `translate.py:main()`，会根据传入的参数调用 `pddl_parser.open()`
      - parse 出来的 programmable pddl 经过 `pddl_to_sas(pddl.Task)` 转变成 `sas_tasks.py:SASTask`，并最终输出到 sas 文件。
  - search: searcher 实现。对外接口是 `planner.cc/main()`，search 过程的入口则在 `search_engine.cc/SearchEngine:search()`
    - `search_engine.h`: 定义了 searcher 的抽象类。
    - search_engines/: 不同 search engine 的具体实现。
- driver: 运行脚本，基本上是为了解决怎么 parse command line arguments，怎么调用可执行文件，怎么输出结果之类。

### Translator
目的是把 PDDL 转变成论文里提到的 Multi-Value Encoding，或者更准确地说，转换成 SAS 格式。
