# InternVLA-N1: An Open Dual-System Vision-Language Navigation Foundation Model with Learned Latent Plans

Shanghai AI Lab.

VLN Foundation Model. 

亮点：

- 完全使用仿真生成数据训练，但是可以直接迁移到现实场景。
- 使用了双系统的设计，一个基于 MLLM-Like 的模型来生成 Navigation 的目标，一个 high frequency local planner 来做 path planning 以到达目标。本文将 high level planner 称为 System 2，而将执行器称为 System 1。

总体方案： System 2 用 MLLM 生成一个图像空间下的目标，即标记一个需要到达的像素点。然后 System 1 用 Local Planner 进行避障和执行。

这其中的核心难点为

1. 两个系统间的协同和同步。
2. 用 2D Pixel Coordinate 作为 Navigation 的目标具有模糊性，可能无法映射到准确的实际目标。
3. （文章没提）真实化渲染。

而本文的解决方案并不是提升同步率与提升 2D Coordinate 准确性，而是通过算法来执行不同步的指令、不准确的 sub-goal。而对于渲染问题，则是使用开源的真实化渲染器。

## Data Generation

本文的训练数据完全由仿真生成，使用的场景来自

- Replica
- Matterport3D
- Gibson
- 3D-Front
- HSSD
- HM3D

数据生成的流程总体为

- 使用3D资产初始化场景。
- 从场景的 Mesh Model 计算 Euclidean Signed Distance Field (ESDF). 这**应该是个 2D 的 Distance Field**
- 使用 A-Star 来随机采样 start and goal points. （应该是随机采样然后 A-Star 过滤）
- 使用 ESDF 来做 Trajectory Optimization.
- 后处理，主要是平滑。

生成 Trajectory 之后，通过 [BlenderProc](https://github.com/DLR-RM/BlenderProc) 渲染得到相片级的图像。BlenderProc 是一个 Blender 渲染 pipeline。现在好像知道HSSD宣传片是用啥做的了。

有了图像还需要生成指令。


