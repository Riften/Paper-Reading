# Combined Task and Motion Planning Through an Extensible Planner-Independent Interface Layer
构建不依赖于特定 Task Planner 或者 Motion Planner 的中间层，实现 TAMP 总体求解。

## Abstract Formulation Using Pose Reference
Task Plan 算法的变量值域通常要求是离散的（例如最常见的 PDDL 表示法中，参数均为离散值），但是事实是想要 Task Plan 能够做到避障、移动障碍物一类的 plan，就必须在问题描述中包含几何信息，而这样的集合信息通常是连续值，而不是离散值。

为了解决这个问题，文章使用了 symbolic references 作为连续值的表示方式，在 Task Plan 中引入几何信息。这种表示方式最早在 **A Hybrid Approach to Intricate Motion, Manipulation and Task Planning** 论文中被提出。

本文中用到的使用判断句 predicates 如下
- $IsGP(pose, obj)$：位于 $pose$ 时，$obj$ 可以被抓住。
- $IsMP(traj, pose_1, pose_2)$：$traj$ 是一个从 $pose_1$ 到 $pose_2$ 的motion plan
- $IsPDP(pose, obj_i, S)$：$pose$ 是一个可以把 $obj_i$ 放到平面 $S$ 上的一个位置。
- $Obstructs(obj', traj, obj)$：抓取 $obj$ 延 $traj$ 运动时，会被 $obj'$ 阻挡。
- $IsPDL(tloc, S)$：$tloc$ 是表面 $S$ 上一个可以放物体的位置。
- $PDObjects(obj', traj, obj, tloc)$：物体 $obj'$ 阻挡了把物体 $obj$ 沿着 $traj$ 放到 $tloc$。


而上述 predicates 定义中用到的参数，其本身应当是连续值。但是为了上述判断句可以应用到 Task Planner 中，就不能直接使用连续值作为参数。所以这里参数的取值范围为定义的一系列有限的 symbolic references。本文中用到的 Symbolic Reference 如下
- 姿态 pose:
  - $initPose$：初始姿态
  - $gp\_obj_i$：可以抓取物体 $obj_i$ 的姿态
  - $pdp\_obj_i\_S$：可以把物体 $obj_i$ 放在表面 $S$ 上的位置。
- 轨迹（Motion Plan）Trajectory
  - $traj\_pose_1\_pose_2$：从 $pose_1$ 到 $pose_2$ 的一个 trajectory

当 predicates 的参数值域取自 symbolic references 的时候，判断句就有了实际的结果值，例如
$$IsMP(traj\_pose_1\_pose_2, pose_1, pose_2) = True\\
IsPDP(pdp\_obj_i\_S, obj_i, S) = True$$

## Interface Layer

## 思考
- 文中列出的 predicates 似乎并不是最简单的，很多判断句实际上可以表示为多个判断句的组合。