# TAMP 参考资料
## 链接
- [Online Course: AI Planning - Univerty of Edinburgh （爱丁堡大学）](http://www.aiai.ed.ac.uk/project/plan/ooc/)：TASK Plan 的网课，基本算法打算直接实现这里面介绍的。
- [Planning Wiki](https://planning.wiki/): PDDL 的 WIKI，包含 PDDL 语法的定义。

## 文章
- [Integrated Task and Motion Planning](./2021%20Integrated%20Task%20and%20Motion%20Planning.md): TAMP 的 Survey
- [Regression Planning Network](./2019%20Regression%20Planning%20Network.md): RPN ，一个用视觉直接做为 State 表示的 Task Plan 方法
- [HTN planning: Overview, comparison, and beyond](./2015%20HTN%20Planning%20-%20Overview,%20comparision,%20and%20beyond.md): 基于 HTN 的 Task Plan 算法的 Survey

其他这个目录里的文章感兴趣的话也可以瞅瞅。

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

