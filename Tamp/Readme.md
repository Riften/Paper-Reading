# TAMP 参考资料
## 链接
- [Online Course: AI Planning - Univerty of Edinburgh （爱丁堡大学）](http://www.aiai.ed.ac.uk/project/plan/ooc/)：TASK Plan 的网课，基本算法打算直接实现这里面介绍的。
- [Planning Wiki](https://planning.wiki/): PDDL 的 WIKI，包含 PDDL 语法的定义。
- [Learning and Intelligent Systems Lab](https://argmin.lis.tu-berlin.de/): 柏林工业大学的实验室。
- [Marc Toussaint 教授的课程](https://www.user.tu-berlin.de/mtoussai//teaching/index.html)
  - [Optimization Algorithm 2020-2021](https://www.user.tu-berlin.de/mtoussai/teaching/20-Optimization/)
- [一些其他学习资料](https://pybullet.org/Bullet/phpBB3/viewtopic.php?f=6&t=63)：Bullet 论坛里有人整理的一些和物理引擎相关的学习资料。

## 文章
- [Integrated Task and Motion Planning](./2021%20Integrated%20Task%20and%20Motion%20Planning.md): TAMP 的 Survey
- [Regression Planning Network](./2019%20Regression%20Planning%20Network.md): RPN ，一个用视觉直接做为 State 表示的 Task Plan 方法
- [HTN planning: Overview, comparison, and beyond](./2015%20HTN%20Planning%20-%20Overview,%20comparision,%20and%20beyond.md): 基于 HTN 的 Task Plan 算法的 Survey

其他这个目录里的文章感兴趣的话也可以瞅瞅。

## 其他笔记
- [AI Planning Course](./AI%20Planning%20Course/README.md): 爱丁堡大学 AI Planning 课程笔记
- [Optimal Control](./Optimal%20Control/README.md): 最优控制相关学习笔记

## 工具
- [ROS2 Tutorial](https://docs.ros.org/en/foxy/Tutorials.html): ROS2 的教程
- [Moveit2 Tutorial](http://moveit2_tutorials.picknik.ai/): Motion Plan 库，初步可能直接使用 Moveit 的 Planning Scene 作为维护场景信息的数据结构
- [Pybind11 Tutorial](https://pybind11.readthedocs.io/en/stable/installing.html)：Bind 到 Python 使用的工具
- [Gecode](https://www.gecode.org/): 当前适用的 Constraint Solver
- [Bullet3](https://github.com/bulletphysics/bullet3): 通用的模拟器，以后可能考虑基于他的 dynamic world 重新实现 planning scene
- [scikit-build tutorial](https://scikit-build.readthedocs.io/en/latest/): 用不用待定，一个可以用 CMake 调用 setuptool 的解决方案，方便直接发行 Python Package。

## 我们的仓库
- [rftask](https://github.com/mvig-robotflow/rftask): 当前代码，后续会重命名为 rfplanner
- [rfpddl](https://github.com/mvig-robotflow/rfpddl): Programmable PDDL 实现，可以作为独立 package 使用
- [Paper-Reading](https://github.com/Riften/Paper-Reading): 我的一部分论文阅读笔记，这个文档也在这个仓库里。

之后会把 pddl 独立成单独仓库。

## 现有 TAMP 系统

名称 | 底层 | 备注 | 核心不足
-- | -- | -- | --
Moveit | Planning Scene | 只包括 Kinematic 信息，提供通用 Planner 接口。实际算法需要实现自己的 Planning Context。| Planning Scene 本身表示能力不足，添加 Dynamics 能力和重新开发没啥区别。
LGP | KOMO | 可以求解 Dynamics 问题，代码相对独立，实现了将不同问题转化为2阶或者3阶优化问题，并用通用求解器求解。| 不是所有问题都能转化为 KOMO。
TrajOpt | OpenRAVE | 基于优化的 Motion Plan 算法，由于算法本身特性，from scratch 的求解 trajectory 可能失败。| 优化方法本身的局限性。OpenRAVE 的复杂环境。
PDDLStream | Fast Download | 本质上是一个 Task Plan 库，用 Stream 作为一个可以求解连续值的 action。 | 不关心算法实现，Stream 实际上依赖于人为设定。

## 目标
让 TAMP 可以求解 Dynamic 问题。

典型 Dynamic 问题

- throw，kick 一类的动作
- 稳定的抓取等 contact 姿态

常见的 Dynamic 信息

- Inertia
- Jacobian
- Contact

### 计划和进度

**开发**

- [ ] Physics Interface: 面向可微引擎设计的接口类
  - [x] JointGroup: Motion Plan 和 Control 的基本单位
- [ ] OMPL 使用，能够做简单地 Sample Based Motion Plan
  - [x] mujoco 简单的测试场景，包含 franka 和障碍物
  - [x] 对 mujoco 进行 collision check
  - [ ] Thread Safe
    - [ ] Simulator pool
  - [ ] State Space
    - [x] 简单地 RealVectorStateSpace 使用
    - [x] 用 Simple Setup 实现 motion plan
    - [ ] KinematicChainSpace 的实现 [OMPL KinematicChainBenchmark](https://ompl.kavrakilab.org/KinematicChainBenchmark_8cpp_source.html)
  - [x] StateValidityChecker
  - [x] Inverse Kinematics
    - [x] mjModel -> Kinematic Info
    - [x] KDL Kinematic Tree 构建
    - [x] Forward Kinematics 计算和检验
    - [x] 求解 IK
      - [ ] 跨越 limit 时出现的 bug
    - [ ] mocap 拖动
- [ ] mujoco 中机器人的基本控制。
  - [x] 基本的阻抗控制 impedance control。
    - [ ] 惯性矩分量计算，避免过大加速度
  - [ ] JointGroup 的控制
  - [ ] 基于速度的控制
- [ ] Optimization 问题构建
  - [ ] Trajectory Optimization: 参考 TrajOpt
  - [ ] Contact(Switch) Optimization：参考 LGP （CIO）
    - [ ] 调研 CIO 的 Matlab 实现
- [ ] 环境交互

**学习**

- Trajectory Optimization
- Contact Invariant Optimization (Motion Synthesis)

**目标**
- [ ] 周 12.3 Sample Based Motion Plan
  - [ ] MuJoCo: Thread Safe Simulator Pool
  - [ ] OMPL: Real Value State Space
  - [ ] OMPL: StateValidityChecker
- [ ] 月 元旦 Contact Invariant Optimization
