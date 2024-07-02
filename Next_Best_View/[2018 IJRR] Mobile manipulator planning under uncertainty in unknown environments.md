# Mobile manipulator planning under uncertainty in unknown environments
- Cite 34

任务场景是一个带有机械臂的可移动机器人（mobile manipulator）在未知的环境中做 motion planning。在场景设置上，强调那些无法在不改变机械臂姿态的情况下就可以凭借 base 直接移动过去的情形。

本文是在其早期工作 Hierarchical and Adaptive Mobile Manipulator Planner (HAMP) 基础上的改进。HAMP 的方法很简单，用两个 sample based planner，一个给 base，一个给 arm，当当前 arm conf 无法让 base path 正常执行的时候，进行 arm reconfiguration。本文的改进针对的是先前没有考虑的 uncertainty。

本文所指的 uncertainty 似乎指的是 base motion 和 arm conf 对彼此影响的 uncertainty。

## Method
