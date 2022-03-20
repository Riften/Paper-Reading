# Optimizer
## Entry
- `data0`: Mesh
- `dx_Elastic`: 弹性形变 $\Delta x_{elastic}$
- `vector<Energy> energyTerms`: 能量项
- `VectorXd searchDir`: 一个 $3n$ 长度的一维向量，3 是 Dimention，n 是顶点数量，也就是 primitive 的数量。 *search direction computed in each iteration。*
- `SpatialHash<dim> sh`: 一个用于快速索引的类，例如索引某个顶点对应的 primitive 
- `vector collisionObjects`: 简单地 model，例如 cube。
- `vector meshCollisionObjects`: `MeshCO` 复杂的 Mesh Model
  
## Methods
`Optimizer(Mesh data, )`:构造函数，最核心的输入是 Mesh Model 和 不同的 Energy 项。
- 构造 能量项`energyTerms`: 初始情况下只有形变能量 `NeoHookeanEnergy`

`precompute`: 计算 solver 依赖的一些固定的矩阵和参数，并且对 solver 的 initial guess 进行设定
- 为线性求解器 `LinSysSolver` 设定输入。线性求解其不负责优化，只是对线性计算的一层封装。输入的是 Vertex 之间的相互关系，相邻节点和面片节点。
- `computeConstraintSets(Mesh)`: 进行一次 CCD 求解，
- `computePrecondMtr(Nesh)`: 
  - 更新节点的连接情况（？
  - `computeDampingMtr`:
    - 计算每个能量项的 Hessian `Energy::computeHessian`，此时能量项似乎只有形变能量。
- `computeEnergyVal`: 计算初始状态能量

`solve`
- `fullyImplicit_IP`: 求解 IP Optimization
  - `initX(option = 0)`: 将 `searchDir` 置零，清空 ActiveSet。 timer "fullyImplicit_eComp"
  - `computeConstraintSets`: 输入当前的 state，输出 active 的 primitive index，这个 index list 就被称为 constraint set。而计算距离的过程称为 evaluate constraint。
    - 重新构建快速索引器 `sh`（由参数控制 `rehash`，看起来只有第一次执行的时候会 rehash）
    - 调用场景中各类不同碰撞体的 `computeConstraintSet` 接口，
      - `Optimizer::collisionObjects.computeConstraintSet`: `collisionObjects` 中只有简单的碰撞体，例如 cube。最后调用到 `virtual CollisionObject::computeConstraintSet`
        - `CollisionObject::evaluateConstraint`: 实际距离计算计算。对于非 Mesh Model，Vertex 只由 index 指定。目前 IPC 只实现了 `HalfSpace::evaluateConstraint`
        - 如果得到的距离小于 $\hat{d}$ (默认配置中为 $1e^{-5}$)，则认为对应的 primitive pair 是 active 的，把对应的 index 加到返回值 constraintset 中。
  - `initKappa`: ???
  - `evaluateConstraints`: 计算 primitive 之间的距离
- timer "solve_extraComp":time integration 计算 X 的更新，以及用 X 的时间差分更新速度和加速度
  - 弹性形变 $\text{dx\_Elastic} = \text{X} - \text{xTilta}$
  - $\text{xTilta} = \text{X\_prev} + \text{velocity}*\text{dt} + g \text{dt}^2$，g 为重力加速度