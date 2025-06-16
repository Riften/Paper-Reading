# VGGT: Visual Geometry Grounded Transformer

CVPR 2025 Best Candidate

Task: 从单帧或多帧（可以上百帧）的图片直接估计
- camera parameters
- point maps
- depth maps
- 3D point tracks

可以用理解为一个很强大的 structure from motion 模型，且只需要任意数量图像输入。

其核心方案是一个足够强大的 ViT Based Backbone，足够多的 multi task 数据。

最终得到的结果可以做到在几十到几百毫秒内完成所有任务。

本文有一些很值得注意的设计
- 模型结构的通用性，没有用专门的 3D 信息处理的模型结构。
- 广泛使用现有 backbone
- 冗余计算，3D Annotation 之间是有映射关系的，比如在已知 camera intrinsics and extrinsics 和 depth map 的情况下就可以计算每个 image 的 point map，但是本文同时输出这些 annotation
- permutation equivariant，模型可以输入任意帧数，且对帧的顺序没有要求，只是会将第一帧作为 reference frame。

## Problem Definition

输入属于同一个 scene 的 N 帧 RGB 图像，对每一帧都输出一系列不同的 3D Annotation

$$
f((I_i)_{i=1}^N) = (g_i, D_i, P_i, T_i)_{i=1}^N
$$

- $g_i\in\mathbb{R}^9$ , camera intrinsics and extrinsics , $[\mathbf{q, t, f}]$ , $q\in \mathbb{R}^4$ 是 4d quaternion， $t\in \mathbb{R}^3$ , translation。 $f\in\mathbb{R}^2$ field of view。可以看出相机参数是简化的，即无形变，相机中心即为画幅中心。这样的简化和 SfM framework 是一致的。
- $D_i \in \mathbb{R}^{H\times W}$ , depth map
- $P_i \in \mathbb{R}^{3\times H\times W}$ , Point map，和 depth map 不同，直接输出 3D 坐标。当输入多帧图片是，其参考系为第一帧图像的相机参考系。
- $T_i \in \mathbb{R}^{C\times H\times W}$ , C-dimensional features for point tracking, 实际的 tracking 由另一个模块输出。


## Model Architecture

### Feature Backbone

非常直接的 Backbone 设计，就是一堆 self-attention only ViT Blocks，外加一点点小设计
- 输入是 DINO patchified image tokens
    - 每一帧 $I$ 都得到 $K$ 个 token $t^I_i\in \mathbb{R}^{K\times C}$，总共得到 $t^I=\cup_{i=1}^N\{t^I_i\}$
- self attention 会分别在两个维度进行
    - 同一个 frame 内的 tokens 之间算 self-attention $t^I_i\in \mathbb{R}^{K\times C}$
    - 所有 tokens 之间算 self-attention $t^I=\cup_{i=1}^N\{t^I_i\}$ 

没有 cross-attention

### Prediction Heads

使用 trainable token 来做输出


