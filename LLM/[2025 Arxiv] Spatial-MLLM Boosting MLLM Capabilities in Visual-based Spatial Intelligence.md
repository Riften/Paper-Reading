# Spatial-MLLM: Boosting MLLM Capabilities in Visual-based Spatial Intelligence

Diankun Wu,  Fangfu Liu, Yi-Hsin Hung, Yueqi Duan

Tsinghua University

Task: 输入 Video，进行空间理解的任务。对于视频会通过一个 space-aware frame sampling strategy 提取包含足够空间信息的帧再进行处理。

Core Idea: 直接使用 VGGT Feature 来提供 Geometry Info。使用单独的模块得到 unified visual token。

本文对于直接训练语言模型来进行空间理解的方法有一个非常简洁的描述

> However, most existing video MLLMs pretrain their visual encoders on image-text pairs—primarily image-caption data [13, 14, 26]—following the CLIP [27] paradigm.

而本文没有遵循以上的范式，即没有通过 image-text data 来训练对图像中所包含的 spatial info 的理解能力，而是直接使用独立的 geometry grounding model 的 feature。和 `image-text` 数据不同，这类模型可以看做是直接使用 `pixel-point` 数据来训练的、不包含语义信息的特征，这与 VLM 自身的信息和能力形成了互补。

架构上比较简单直接，使用两个 encoder 来处理语义和几何信息。

## Related Work

Related Work 部分对现有的多模态输入的方式进行了一个总结

- token-level fusion: BLIP-2, Flamingo, Q-Former
- feature-level fusion: cross-attention
- MLP Projection: LLaVA, MiniGPT-4

而在视频处理方面，MLLM 方法还在着重理解时序信息，对于空间理解依然非常薄弱。

## Methodology

![SpatialMLLM](../imgs/SpatialMLLM.png)

- 输入 N 帧 $\mathcal{V} = \{f_i\}_{i=1}^N$
- 从中均匀选取 $N_m$ 帧输入 VGGT，得到这些帧的 depth 和 camera pose
- 按照最大 voxel coverage 进一步降采样到 $N_k$ 帧，作为最终参与 MLLM 的图像。
- 将图像输入 VGGT，取其 vision feature $e_{3D}$。而 camera 和 register 位置对应的 feature 被抛弃了。文章没有详细说明 vision feature 是如何选取的，考虑到 VGGT 使用 DPT Head 来进行 dense 输出的特性，直接取最后一层的特征可能并不是个很好的方法，这样和直接用 camera token 没啥区别。
- 特征融合部分则首先将 $e_{3D}$ 重组成 $e_{2D}$ 相同的尺寸，文章没有提这里的 $\text{Rearrange}$ 具体做法。
- 用两个 MLP 得到最终 LLM 部分使用的 Feature $e=\text{MLP}_{2D}(e_{2D}) + \text{MLP}_{3D}(e'_{3D})$

本文的数据集也是自己构建的，包含 120K 条 QA 数据。其中的问题包含距离、大小、方向、路径规划等等。最终得到的效果实际上并没有特别显著的优于其他 MLLM。