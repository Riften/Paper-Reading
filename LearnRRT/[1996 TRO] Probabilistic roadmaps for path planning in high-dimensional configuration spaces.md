# Probabilistic roadmaps for path planning in high-dimensional configuration spaces
PRM Planner.

PRM 在 Learning Phrase 有额外的策略保证尽可能包含 C-Space 中比较困难的部分。PRM 最终生成的 Roadmap 并不是均匀分布的。

RoadMap Construction Process
- R=(N,E)
- 从 $C_{free}$ 采样 $c$ 加到 $N$
- 从 $N$ 选择一部分节点，测试其与 $c$ 的连接性（可以是任意速度足够快的 local planner），如果找到了 path $(c,n)$，则将该 edge 添加到 $E$
    - 节点选择策略：选择与 $c$ 一定距离 $D$ 内的节点 $N_c$，从近到元检查与 $c$ 的连同性（如果因为前面的检查使得节点已经连通，则不再寻找直连的 path）。

构建过程基本上是随机采样，额外加了一点设计来减少对 local planner 的调用。

RoadMap Extension，目的在于增强 R 的连通性。文中所说的 "Difficult Region" 也在这里体现，即 $C_{free}$ 中连通但是 $R$ 中不连通的区域就是相对 "Difficult" 的，需要一定的 heuristic 来增加在这部分区域的采样率。这里的 "Expand" 指的是在某些现有 configuration 周围采样。

为了提升在 difficult region 的采样率，为每个节点赋了一个权重，权重计算规则
- construction 期间，维护每个节点的连接失败率 $r_f(c) = \frac{f(c)}{n(c)+1}$，n 是尝试连接次数，f 是其中失败的次数。
- expansion 开始时将 $r_f$ 归一化作为采样权重 $w(c)$
- 选择好扩展节点之后，expand 的方式起了个名 random-bounce walk，其实就是选一个方向一直扩展到 collision 为止。
- 如果从 c 一直扩展到 n，之后发生 collision，则把 (c, n) 加入到 R 中，并且尝试将 n 与其他连通域连接，方法也是选取与 n 一定距离内的节点。
- expansion 过程中不更新权重。