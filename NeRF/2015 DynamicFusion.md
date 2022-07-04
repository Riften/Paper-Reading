# DynamicFusion: Reconstruction and Tracking of Non-rigid Scenes in Real-Time
对柔性物体的3D重建。

基本想法是，对于一个形变物体的一系列 frames，将所有形变后的模型统一到一个 canonical frame，就可以用对 rigid 物体的 fusion 方法来做重建。而且如果每一帧的 frame 和 canonical frame 之间的转换时知道的，是可以将重建后的模型重新映射回每一帧的姿态的。

## Dense Non-rigid Warp Field
算法核心，对形变物体 motion 的建模。

最简单的想法是，将物体的 motion 用模型中每一个点的 pose (transformation) 来表示，文中将这个转换成为 warp function

$$\mathcal{W}:\mathbf{S\rightarrow SE(3)}$$

需要注意的是，如果仅仅是对坐标点而言，用平移来描述位置变换即可。但是在本文中，既使用了平移也使用了旋转。

而 warp function 的定义需要满足：
- 能够描述 dense deformation，即每个模型上的点都可以计算对应的 transformation。
- 计算复杂度不能太高，例如 voxel 的方法就不可行。

文中的做法是直接借助CG里面的用更少参数渲染 蒙皮motion 的算法，Dual-Quaternion Blending (DQB)。

具体来说，模型的 motion 由 n 个 deformation node 决定，对于每个 node $i$，其在时刻 t 的 motion 由三个参数决定
- $\mathbf{dg}_v^i\in \mathbb{R}^3$: 该点在 canonical frame 中的坐标
- $\mathbf{dg}_{se3}^i$: 对应的 transformation matrix $T_{ic}$
- $\mathbf{dg}_{w}^i$：基于半径的权重项，每个 deformation node 对其周围的 point 的影响权重由该项计算出来，对于点 $x_c$，权重为 $\mathbf{w}_i(x_c) = \exp(-\lVert \mathbf{dg}_v^i-x_c \rVert^2/(2(\mathbf{dg}_w^i)^2))$

整个模型在 t 时刻的 warp-field 由所有 deformation node 的这三个参数决定

$$\mathcal{N}_{\mathbf{warp}}^t = \{\mathbf{dg}_v, \mathbf{dg}_w, \mathbf{dg}_{se3}\}_t$$

可以计算任意一个模型上的点在 warp-field 中的位置、norm 等值。