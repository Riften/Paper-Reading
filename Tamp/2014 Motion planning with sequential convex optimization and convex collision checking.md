# Motion planning with sequential convex optimization and convex collision checking
- [trajopt: 文章实现的 Solver](https://rll.berkeley.edu/trajopt/doc/sphinx_build/html/)
- [trajopt github](https://github.com/joschu/trajopt)

一个在 CHOMP 基本思想上改善的基于优化的 Motion Plan 方法。

传统的 Motion Plan 算法总体上是基于随机采样的，可以在一定概率上保证得到结果。

但是基于采样就意味着 Trajectory 中不包含速度等信息，需要经过后处理来进行路径的平滑，通常也会经过一个优化算法来缩短路径。代表性的 RRT 算法是 2011 年提出来的。

## 问题定义
在 Kinematic Motion Planning 问题中，优化的变量维度可以看作 $T\times K$，其中 $T$ 是 time-steps 数量，$K$ 则是可控制的 DOF 维度，可以认为是转轴数量。

这些变量也可以看作是 $x_{1:T}$，$x_t$ 代表在第 $t$ 个 timestep 的 configuration。

目标函数可以根据需求来设计，例如如果想要得到最短路径，可以直接使用

$$f(x_{1:T}) = \sum_{t=1}^{t-1}||x_{t+1} - x_t||^2$$

常见的 constraint 包括
- Equality Constraints:
  - end-effector 的位置
  - end-effector 的角度，例如保持某个方向。
- Inequality Constraints:
  - joint limits
  - speed limits
  - **obstacle avoidance**

## No-collision constraint
对于 $A,B,O$ 三个刚体，他们所包含的坐标点集合为 $\mathcal{A,B,O}$。他们之间的距离定义为

$$\text{dist}(\mathcal{A,B}) = \inf\{\Vert T\Vert \big| (T+\mathcal{A})\cap \mathcal{B}\neq \emptyset\}$$

这里的 inf 是指 infimum，下确界。这个距离定义也被称为 minimum translation distance，即想要让两个点集有交集的最小的 translation。类似的还可以定义 入侵深度

$$\text{penetration}(\mathcal{A,B}) = \inf\{\Vert T\Vert \big| (T+\mathcal{A})\cap \mathcal{B} = \emptyset\}$$

再次基础上文章定义了一个 signed distance

$$\text{sd}(\mathcal{A, B}) = \text{dist}(\mathcal{A,B}) - \text{penetration}(\mathcal{A,B})$$