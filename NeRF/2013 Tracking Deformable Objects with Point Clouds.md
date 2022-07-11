# Tracking Deformable Objects with Point Clouds
核心任务是在 simulation 中追踪当前操作物体的状态，消除遮挡等因素的影响。

整个实验环境是非常理想的环境，并且模型也是事先建模而不是重建。

给定一个模型，模型在 simulator 中的顶点坐标为 $m_{1:K}$。在每个 timestep，会采集一个 pointcloud $c_{1:N}$。而本文的目的是，根据 $c$ 的值，计算每个 timestep 的 $m$ 的值。