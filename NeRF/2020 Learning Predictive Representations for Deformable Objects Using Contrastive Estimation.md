# Learning Predictive Representations for Deformable Objects Using Contrastive Estimation
CFM （Contrastive Forward Modeling）

核心想法是用对比学习来直接在 latent space 学习 deformable objects 的 dynamic model。任务场景仍然是展开衣服或者绳子这样的不包含语义的任务。

本文的模型分为两大部分：
- encoder - decoder : 用 reconstruction 作 superviser 来训练 auto encoder。
- encoder - dynamic predictor : 直接在 latent space 用 MLP 作 dynamic model。

上述两部分在训练的时候是一起训练的。除此之外，为了让 latent space 的分布与输入的分布更接近，额外使用 contrastive loss 来训练一个二分类器（分类两个输入是否为 action 对应的结果，分类器输入也是 encoder 输出的 latent vector），以此来调整 latent space 的分布。

## Contrastive Forward Modeling
![](../imgs/CFM.png)

学习的过程直接照搬了 [CPC](./2018%20Representation%20Learning%20with%20Contrastive%20Predictive%20Coding.md)


## Running Code
- protobuf 版本有问题，需要在运行时加上 prefix `PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION=python`