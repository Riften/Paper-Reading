# 3D Neural Scene Representations for Visuomotor Control
把不同视角的图片映射到同一个视角无关的 latent state representation, 并且能够根据当前的 latent 和 action 输出新的 latent space.

另外，为了让模型可以从没见过的视角的图片一样的到 latent state，额外增加了一个 decoder。decoder 负责将 state 重新解码为 image。当输入一个没见过的视角的图片的时候，通过计算 decoder 输出和 encoder 输入之间的 L2 distance，将该 distance 的梯度传播到 state 上，更新 state 来最小化 distance，从而得到没见过的视角下图片对应的 state。文中把这个模块称为 auto-decoding inference-via-optimization framework.

## 3D Aware Scene Representation Learning
