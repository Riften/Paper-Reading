# Streaming 4D Visual Geometry Transformer
Dong Zhuo*, Wenzhao Zheng*†, Jiahe Guo, Yuqi Wu, Jie Zhou, Jiwen Lu

一个 Incremental 的 VGGT，训练则是一个基于蒸馏 distillation-based 的训练方案。

VGGT 原本的 Frame 之间的关联性是逐渐构建的，这样的架构本身是没办法增量调用的，每一帧在模型内部都是和其他帧关联的。为了能够增量使用，本文将原本的重建任务重新建模成了一个 encoder decoder 的架构，这样新来的 frame 只需要与 encoder 得到的其他 frame 的 embedding 进行 attention。

$$
F_t = \text{Encoder}(I_t) \\
G_t = \text{Decoder}(F_t) \\
(P_t, C_t) = \text{Head}(G_t)
$$

Decoder 是一个 Multi-view Decoder，得到的 $G_t$ 即是包含该帧所有几何信息的 Geometry Token，最终的 Output Head 只是 MLP。

## StreamVGGT

修改 VGGT 使其满足上面的 encoder decoder 建模方式，简单说就是在原本 VGGT 上加上 Attention Mask，给图像添加顺序，要求每一帧只能 attend to itself and its predecessors.

即从下面的式子1到式子2

$$
\{G_t\}_{t=1}^T = \text{Decoder}(\text{Global SelfAttn}(\{F_t\}_{t=1}^T))\\
\{G_t\}_{t=1}^T = \text{Decoder}(\text{Temporal SelfAttn}(\{F_t\}_{t=1}^T))
$$

实际的实现方式是只修改了 VGGT Alternative Attention 中的 Global Self Attention 部分。

看上去其他部分，不管是 DPT Head 的设计，还是 Camera Head 的设计都没有变。

