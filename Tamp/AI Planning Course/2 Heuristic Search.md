# State-Space Search: Heuristic Search and STRIPS

## Heuristic Search 启发式搜索
启发式搜索的核心是启发函数 Heuristic Function。启发函数给出了从一个节点到目标节点的最短路径（cost最小路径）的 cost 估计。

## Best-First Search 最佳优先搜索
在前面提到的搜索算法中，`selectFrom` 函数是算法的核心之一，而最佳优先搜索就是根据启发函数，每次选择最佳的节点。

其最直接的实现方式是用一个优先级队列维护 `fringe`。

需要注意的是，最优先搜索使用的 evaluation function 并不总是 Heuristric function 本身。如果直接用 Heuristic function 作为 evaluation function，那么这样的算法称为 Greedy Best-First Search。

但是贪心算法并不能保证最优解，核心点在于 Heuristic Function 往往只能是一个估计值，而且可能和真实值偏差悬殊。

## A* Algorithm
和 Best-First 相比修改了 Evaluation Function $f(n) = h(h) + g(n)$
- $h(n)$ 还是启发函数，也就是从 $n$ 到目标节点的开销估计。
- $g(n)$ 是到达节点 $n$ 的开销。

## Admissible Heuristics
如果启发函数的估计值永远不超过实际值，那么称启发函数是 admissible。$h(n) \leq$ actual dististance to the nearest goal node. 这时候一条 Path 上的 Evaluation Function 值是非减的。

**A\* 的正确性和完备性**：如果 Search 问题存在最优解，使用 admissible heristics function 的 A* Tree Search 算法结果是最优的。

当 Heuristic Function 值为 0 的时候，A* 算法也就变成了 **Dijkstra** 算法。

**A\* 的效率最优性**：对特定的启发函数来说，没有算法可以比 A* 访问更少节点的情况下保证 Search 到 Goal 节点。

A* Tree/Graph 搜索算法伪代码
> **function** aStarTreeSearch($problem$, $h$)
> - $fringe \leftarrow$ priorityQueue(**new** searchNode($problem$.initialState))
> - $allNodes\leftarrow$ hashTable($fringe$)
> - **loop**
>   - **if** empty($fringe$) **then return** failure
>   - $node \leftarrow$  selectFrom($fringe$)
>   - **if** $problem$.goalTest($node.state$) **then**
>       - **return** pathTo($node$)
>   - **for** $successor$ **in** expand($problem$, $node$)
>       - **if not** $allNodes$.contains($successor$) **then**
>           - $fringe\leftarrow fringe+successor$ @f($successor$)
>           - $allNodes$.add($successor$)

上述算法里面与 hashtable 相关的内容是为了让算法适用于 Graph Search。

A*算法的效率并不高，它的时间和空间复杂度都是 $O(b^l)$，$b$ 是每个节点平均的后继节点数，$l$ 是 path 的长度。

## Relexed Problem
通过放宽原本 action 的限制，可以得到一个不同于原本问题的 Relexed Problem。而 Relexed Problem 的最优解肯定不会比原问题的最优解有更高的 cost ，毕竟限制都放宽松的，目标也应该更容易达到。

所以一个 Relexed Problem 的 Cost 对于原问题来说，就是一个 Admissible 的 Heuristic Function。