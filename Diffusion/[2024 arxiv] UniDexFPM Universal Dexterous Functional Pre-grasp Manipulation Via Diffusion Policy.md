# UniDexFPM: Universal Dexterous Functional Pre-grasp Manipulation Via Diffusion Policy
解决的task是 pre-grasp manipulation，将抓取的物体的位姿调整到谋个适合抓取的（已知的）位姿。

比较独特的是算法上使用了 diffusion model 和 RL，其对点云数据的处理、对diffusion model 生成 action 的方式都值得借鉴。

用 RL 来做灵巧操作 dexterous manipulation 是常见做法，但是通常需要为不同任务设计特定的 reward，同时还非常容易陷入 local optim。本文实际上并没有从根本上解决这个问题，只是对 reward 的定义更加 general，并且避免单个 reward 项独自收敛到最小。

## Method
算法的总体上是 teacher-student 的学习框架，而 student policy 部分是一个用 diffusion model 充当的生成模型。