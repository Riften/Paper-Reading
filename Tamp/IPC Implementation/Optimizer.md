# Optimizer
`Optimizer(Mesh data, )`:构造函数，最核心的输入是 Mesh Model 和 不同的 Energy 项。

`precompute`: 计算 solver 依赖的一些固定的矩阵和参数，并且对 solver 的 initial guess 进行设定
- 为线性求解器 `LinSysSolver` 设定输入。线性求解其不负责优化，只是对线性计算的一层封装。输入的是 Vertex 之间的相互关系，相邻节点和面片节点。
- `computeConstraintSets(Mesh)`

`solve`
- a