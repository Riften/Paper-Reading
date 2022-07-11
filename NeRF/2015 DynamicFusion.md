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

具体来说，模型的 motion 由 n 个 deformation node 决定，对于每个 deformation node $i$，其在时刻 t 的 motion 由三个参数决定
- $\mathbf{dg}_v^i\in \mathbb{R}^3$: 该点在 canonical frame 中的坐标
- $\mathbf{dg}_{se3}^i$: 对应的 transformation matrix $T_{ic}$
- $\mathbf{dg}_{w}^i$：基于半径的权重项，每个 deformation node 对其周围的 point 的影响权重由该项计算出来，对于点 $x_c$，权重为 $\mathbf{w}_i(x_c) = \exp(-\lVert \mathbf{dg}_v^i-x_c \rVert^2/(2(\mathbf{dg}_w^i)^2))$

整个模型在 t 时刻的 warp-field 由所有 deformation node 的这三个参数决定

$$\mathcal{N}_{\mathbf{warp}}^t = \{\mathbf{dg}_v, \mathbf{dg}_w, \mathbf{dg}_{se3}\}_t$$

可以计算任意一个模型上的点在 warp-field 中的位置、norm 等值。

对于任意一点 $x_c$，其在k个 deformation node 定义的 warp-field 对应的 warp function 为

$$
\begin{aligned}
    \mathcal{W}(x_c) &= SE3(\mathbf{DQB}(x_c))\\
    \mathbf{DQB}(x_c) &= \frac{\sum_{k\in N(x_c)}\mathbf{w}_k(x_c)\hat{q}_{kc}}{\lVert \sum_{k\in N(x_c)}\mathbf{w}_k(x_c)\hat{q}_{kc} \rVert} 
\end{aligned}
$$

这里的 $N(x_c)$ 即 $x_c$ 最近邻的 $k$ 个 deformation node。

本文的场景中不仅仅考虑了物体形变，也考虑了摄像机移动，所以完整的 warp function 还需要加上摄像机的移动项 $T_{lw}$

$$\mathcal{W}_t(x_c) = T_{lw} SE_3(\mathbf{DQB}(x_c))$$

有了 motion field 的定义，剩下的核心工作是
- estimate motion field
- 根据 motion field 来把多帧 fusion 在一起

## Estimate Warp-Field State $\mathcal{W}_t$
通过最小化以下两项得到
$$
E(\mathcal{W}_t, \mathcal{V}, D_t, \mathcal{E}) = \mathbf{Data}(\mathcal{W}_t, \mathcal{V}, D_t) + \lambda\mathbf{Reg}(\mathcal{W}_t, \mathcal{E})
$$
- $\mathbf{Data}(\mathcal{W}_t, \mathcal{V}, D_t)$ 是ICP 项，衡量 warp field 和 depth data 之间的 matching 程度
- $\mathbf{Reg}(\mathcal{W}_t, \mathcal{E})$ 是平滑项，保证形变一定程度的平滑。

## Dense Non-Rigid Surface Fusion
最终的重建结果是 truncated signed distance function TSDF 的形式。所以在得到每个时刻的 warp field 之后，还需要根据这个 field 将不同视角的 SDF fusion 成一个。

TSDF $\mathcal{V}$将一个坐标值映射到一个 Signed Distance 和一个 confidence。

$$\mathcal{V}: \text{S}\mapsto\mathbb{R}^2$$

其中这里的 $\text{S}$ 是由 Voxel 表示的，而不是 implicit neural represent 那种连续表示，这也是用 TSDF 而不是 SDF 的原因，因为得到的只能是离散值，所以需要 TSDF 做插值得到精确的 SDF。

合并不同帧 TSDF 的基本逻辑是把每个 voxel 的 TSDF 值根据 warp-field 转换到 live frame，然后和 depth 数据计算差别决定是否更新。