# Batch Informed Trees (BIT*): Sampling-based optimal planning via the heuristically guided search of implicit random geometric graphs
## Related Algorithm
### A*
图搜索算法。

最简单的 Heuristic Search，维护当前 search 过的所有 node 以及从 start 到这些 node 的 cost $g(\cdot)$，定义一个 heuristic function $h(\cdot)$ 衡量任意 node 到 goal 的预估 cost，$h(\cdot)$ 的估计值要比真实值低。

A* 算法每次选取 $f(n)=g(n)+h(n)$ 值最小的节点作为扩展节点，将该节点所有的 neighbors 加入 search tree，并且计算这些 neighbors 的 g,h 值。当选取的 f 最小的节点为 goal 节点的时候，算法结束。或者当队列空了的时候，说明没有路径。

Psedocode
```python
priority_queue = {n_start}
g[n_start] = 0, g[others] = infinity
f[n_start] = h(n_start), f[others] = infinity
while len(priority_queue) > 0:
    n_current = priority_queue.pop() # pop the node with lowest f
    if n_current == n_goal: 
        return True
    for n_neighbor in neighbors(n_current):
    if cost(n_current, n_neighbor) + g[n_current] < g[n_neighbor]:
        n_neighbor.from = n_current
        g[n_neighbor] = cost(n_current, n_neighbor) + g[n_current]
        f[n_neighbor] = g[n_neighbor] + h(n_neighbor)
        if n_neighbor not in priority_queue:
            priority_queue.push(n_neighbor)
return False
```

### LPA*, Lifelong Planning A*
LPA* 是对 A* 进行一定的改进，其目的是当一部分节点的 edge cost 发生改变的时候，不用重新进行 A* search。

LPA* 为每个节点维护两个 cost 值
- $g(\cdot)$: start distance, 和 A* 里面一样，是从 start 一直到该节点的累积 cost
- $rhs(\cdot)$: lookahead start distance，为该节点所有父节点的 g 加上之间 edge cost。$rhs(n) = \min_{n'\in \{\text{The direct predecessors of n}\}}(g(n') + d(n', n))$. 这里的 predecessor 不是从 search tree 中采取，例如采样到了一些新的节点，那么可以取一定距离内 collision free 得直连到 n 的节点。

对于一个节点，如果 $rhs(n)\neq g(n)$，则称该节点 locally inconsistent。LPA* 的基本逻辑是，当部分节点之间的 cost 发生改变之后，会有部分节点变得 locally inconsistent。这时候只需要找出所有 locally inconsistent 的节点，计算他们的 g() 和 rhs()。

LPA* 使用的 priority_queue 使用一个2D key
$$
k(n) = \left[\begin{aligned}k_1(n)\\ k_2(n)\end{aligned}\right] = \left[\begin{aligned}&\min(g(n),rhs(n)) + h(n, goal) \\ &\min(g(n), rhs(n))\end{aligned}\right]
$$

按照上述优先级处理 inconsistent 的节点。至于为啥这样处理就能保证找到最短路径以及 loop 的 end condition 有点绕先不管。


### BIT*
A* 没办法直接用于 continuous space planning。把 LPA* 在 A* 上的优化思路用到 RRT* 上，就是 BIT* 的基本逻辑。

continuous space planning 的一种 planning 方式便是采样，采样到的节点之间有 collision 以及距离关系。这样由随机采样的 state 和 geometry 关系定义的 graph 在文章中称为 random geometric graph RGG。RGG 可以被看作是一个 implicit graph，而从 RGG 采样计算之后构建的 search tree 则是 explicit graph。两种常见的 RGG edge 的定义方法是 k-nearest 和 r-disc。

从 RGG 的角度看 RRT*，sample 过程和 search 过程是同时进行的，即从 RGG 中进行采样之后，就会进行 reconnect 和 rewire 来保证 local optimal。

而 BIT* 则是逐渐提升 RGG 的 resolution。总体思路是
- 采样一个 batch 的 state，通过 heuristic search 找到 solution
- 更新 k-nearest sample 的 k 值或者 r-disc sample 的 r 值，采样更密集的 sample，这里可能采用 informed sampling.
- 用类似 LPA* 的方式，在原有 search tree 的基础上把新采样的 state 加上去。

具体来说
- 如果已经有了一条 cost 为 $c$ 的 solution path，则先对 search tree 进行剪枝。剪枝的目的是对于那些 inconsistent 的 vertex 需要重新进行 expansion。
    - 删掉所有 $f\geq c$ 的节点
    - 删掉这些节点所有相关的边
    - 更新 g 值，删掉所有 $g=\inf$ 的节点，并把这些点看作是新采样的点。
- 把剪枝之后的 search tree 所有节点放到一个优先级队列 $Q_V$ 里面，其优先级的 key 为 $g(v)+h(v)$，即经过该节点的路径的 cost 估计值，越小优先级越高。
- 将 $Q_v$ 中的节点按照优先级向 $X_{samples}$ 拓展
    - $v \leftarrow Q_V.pop()$
    - $X_{near}\leftarrow \{X_{samples} | \lVert x-v\rVert^2\leq r\}$，找到新采样点中距离 v 一定范围内的节点
    - 如果加上 (v, x) 这条边有可能提升 solution，即 $g(v) + c(v,x) + h(x)\leq g(x_{goal})$，则把 (v, x) 加到一个边的优先级队列 $Q_E$ 里，其 key 和 $Q_V$ 类似，为 $g(v) + c(v, x) + h(x)$，即经过这条边的 solution 的 cost 估计值。
    - 如果发现优先级最高的 $v\in Q_V$ 对应的 cost 估计值比 $Q_E$ 中最小的 cost 要大，则停止向 $Q_E$ 中添加 edge。原因是因为经过 $Q_V$ 中剩下节点的最优路径已经比不上经过 $Q_E$ 的最优路径了，
- $(v_m, x_m)\leftarrow Q_E.pop()$，接下来尝试将该 edge 加到 search tree，判断依据是加上这个 edge 会不会优化 solution 以及优化到 x_m 的 cost
    - 首先c, h 都使用 heuristic，check 其 cost 下限是否有优化 solution 的潜力。 check $g(v_m) + \hat{c}(v_m, x_m) + \hat{h}(x_m) < g(x_{goal})$
    - 然后实际执行 collision check 等，求出 $c(v_m, x_m)$，再检查是否 $g(v_m) + c(v_m, x_m) + \hat{h}(x_m) < g(x_{goal})$
    - 最后如果却是其有潜力提升 solution，再 check $g(v_m) + c(v_m, x_m) < g(x_m)$
    - 把 edge 加到 search tree。此时如果 edge 连接到的是一个原本不属于 
- 重复上面两步直到两个队列为空，此时可以开始 sample 下一个 batch。换言之两个队列实际上是交替 pop 的，并且这个过程中会互相向另一个队列添加元素。当队列为空时，说明当前的所有 vertex 已经没有可以 expansion 的点（剩下的 samples 距离现有 search tree 太远），以及当前的 edge 已经不会有更优的 rewire。

## Implementation
核心实现在 `BITstar::iterate`，队列的实现在则在一个单独的 inner class `BITstar::SearchQueue`。`SearchQueue` 并不是单纯的队列，而是封装了算法中 $Q_V$ 和 $Q_E$ 以及其所有操作。
- 判断队列是否为空
- 队列非空则尝试 expand edge
    - 从 $Q_E$