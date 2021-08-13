# Moveit! Task Constructor for Task-Level Motion Planning
这篇文章的目的简单说就是 “让 MoveIt 能够执行 Task Plan 的结果”。

对于 Task Plan 任务而言，Plan 过程中不能牵扯到 continuous value 的求解，所以任务中所有参数都是离散化的 symbolic reference。这篇文章就是为了填补 Task Plan 的结果和实际执行之间的空缺。

- [github](https://github.com/ros-planning/moveit_task_constructor)
- [tutorial](http://moveit2_tutorials.picknik.ai/doc/moveit_task_constructor/moveit_task_constructor_tutorial.html)

**Note:** 虽然给出了 ROS2 Tutorial 的链接，但是实际上该库尚不支持 ROS2。

## 基本概念

