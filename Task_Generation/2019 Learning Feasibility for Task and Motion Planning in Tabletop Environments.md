# Learning Feasibility for Task and Motion Planning in Tabletop Environments
Task and Motion Plan 的算法通常是先找 high-level task plan 然后做 motion plan。但是实际 plan 过程中，一个很大的时间浪费是执行那些 infeasible 的 action。

文中训练了一个 classifier 来快速决定一个 high-level 的 action 是不是可执行的，只有在分类器说可以执行的时候才进行实际的 motion plan。