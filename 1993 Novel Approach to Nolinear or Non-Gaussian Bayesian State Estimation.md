# Novel approach to nonlinear/non-gaussian bayesian state estimation

提出Particle Filter，用来解决状态估计问题。

## 概念：贝叶斯滤波
- [知乎：贝叶斯滤波](https://zhuanlan.zhihu.com/p/37028239)
- [Wikipedia: Recursive Bayesian Estimation](https://en.wikipedia.org/wiki/Recursive_Bayesian_estimation)

贝叶斯滤波的研究对象是概率机器人，概率机器人用概率分布的形式看待机器人在整个推测空间中的状态。这个状态的置信度分布可以看做是一个关于观测量、控制量、和时间的一个后验分布
$$bel(x_t)=p(x_t | z_{1:t},u_{1:t})$$
$x_t$表示$t$时刻的状态，$z_{1:t}$表示至今为止的观测量，$u_{1:t}$表示至今为止的控制量。

有了这个分布关系，那么就可以进行两类计算

**控制更新，Prediction**：基于状态的分布，可以实现对于状态的预测
$$\bar{bel}(x_t)=p(x_t | z_{1:t-1},u_{1:t})$$
即根据之前的观测量，和当前的操作量，预测经过操作之后的状态分布。

**测量更新，Innovation**：得到前面的状态预测之后，再进行实际的操作和观察，得到新的观测量$z_t$，并基于此对预测进行修正，得到状态量新的置信度。

所谓的贝叶斯滤波算法便是迭代进行上述两个过程
- 通过控制量预测$\bar{bel}(x_t)$
- 结合观测量修正$bel(x_t)$

对于机器人领域来说
- $x$状态量代表机器人的位置、姿态、运动信息等
- $z$观测量代表传感数据，例如视觉、触觉、运动等
- $u$控制量即Action

贝叶斯滤波让机器人能够 根据其最近获取到的传感数据，持续更新其在坐标系统中位置的最大似然估计（或者其他状态信息）的能力。