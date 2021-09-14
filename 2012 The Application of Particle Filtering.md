# The Application of Particle Filtering to Grasping Acquisition with Visual Occlusion and Tactile Sensing

解决在机械臂抓取场景中，同时追踪抓取物体并且估计动态参数的问题。

## 关于 Filtering Problem
- [Wikipedia: Filtering Problem](https://en.wikipedia.org/wiki/Filtering_problem_(stochastic_processes))
- [Wikipedia: Stochastic Process](https://en.wikipedia.org/wiki/Stochastic_process)

是随机过程（Stochastic Process）中的一类问题，指从对系统的一个不完整的、充满噪声的观测中建立一个对系统的最佳估计的问题。

## Introduction
提出的问题场景叫做$G-SL(AM)^2$问题
- G指Grasping，最终是为了更好的解决抓取问题
- $SL(AM)^2$指Simultaneous Localization, and Modeling, and Manipulation
    - Modeling: 让机器人根据传感系统（触觉、视觉、运动）来建立和优化物体的模型
    - Manipulation: 机器人会通过物理上操作物体来帮助完成建模任务
    - Localization: 机器人会在抓取和操作物体的过程中追踪物体姿态
    - Simultaneous: 所有上述任务是同时实时进行的。