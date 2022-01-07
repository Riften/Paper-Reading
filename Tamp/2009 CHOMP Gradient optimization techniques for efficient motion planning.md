# CHOMP: Gradient optimization techniques for efficient motion planning
trajectory cost 由两部分组成
$$\mathcal{U}(\xi) = f_{prior}(\xi) + f_{obs}(\xi)$$
- $f_{obs}$: obstacle term，衡量 trajectory 接近 obstacle 的程度
- $f_{prior}$: prior term，衡量 dynamical 的合理性，例如平滑度和加速度限制。

## prior term
简单说就是多阶差分的 square，目的是对 trajectory 做平滑。

## obstacle term

## Implementation

`ChompPlanner::solve(planning_scene, motion_plan_request, chomp_param, motion_plan_response)`

### 创建并初始化 trajectory
- 创建 `ChompTrajectory`，是一个离散化的 joint-space trajectory，核心是一个 `(n_p, n_j)` 维的 Eigen Matrix，`n_p` 是离散点的数量，`n_j` 是转轴数量。
  - 创建时指明了 trajectory 的时长 `duration = 3.0`，以及离散点之间的间隔 `discretization = 0.03`
  - `number_joints = end_index - start_index + 1 + extra_points`，trajectory 的两端可以有固定不变的额外点，用于计算梯度
- 把起始状态（planning_scene 的当前状态） copy 给 trajectory 的第一个离散点
- 从 request 里面读出来每个转轴的角度，构造 `goal_state`。 **ChompPlanner 只支持 joint-space goal constraint，即指定目标姿态**。
- 把 goal_state copy 给 trajectory 最后一个离散点
- 对于可以自由旋转一周的 joint 专门计算 start 到 goal 之间最近的旋转方向
- 用插值的方法把 trajectory 的中间点填满

### 优化 trajectory
`ChompOptimizer(trajectory, planning_scene, group_name, chomp_param, start_state).optimize()`

- 初始化 Optimizer
  - 由于使用了差分计算梯度，所以需要在原本 trajectory 两边填充一部分点。这部分点在优化过程中值保持不变，不算在 start 到 end 的范围内，因为其唯一作用是计算梯度。但是数量包含在 `num_points` 里面
  - 从 planning_scene 获取 collision detector
  - **moveit 实现了 collision distance field**，可以直接获取 collision 的梯度。`CollisionEnvHybrid::getCollisionGradients`