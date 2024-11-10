# Energy-Based Diffusion Language Models for Text Generation

TASK: 对使用 Diffusion Model 的 LLM 进行加速，和其他基于 Diffusion Model 的 LLM 相比，采样速度大概是 1.3 倍。

基于 Diffusion Model 的 LLM 和 GPT 等自回归模型的区别在于 Diffusion Model 对整个句子进行 denoise，而自回归模型则是一个词一个词生成。

本文的理论基础来自于 [2021 NeurIPS: Structured Denoising Diffusion Models in Discrete State-Spaces](./[2021%20NeurIPS]%20Structured%20Denoising%20Diffusion%20Models%20in%20Discrete%20State-Spaces.md)

从左到右按词生成的问题：
- 慢
- 累计误差

> In this work, we take a closer look at the existing discrete diffusion models and reveal a vital mismatch issue between the training and sampling distribution in its current form. Specifically, the existing discrete diffusion models aim to predict all missing tokens in parallel at each intermediate diffusion step, but the denoising joint distribution is simply parameterized as a product of token-wise independent distributions

TOREAD: Related work about Discrete Diffusion Model

## Discrete Diffusion Model


## Thinking & Inspiring
Diffusion Model 作用在 sequence ，对于每一个 waypoint 之间的关联性是没有进行建模的，相反，这种关联性是由卷积等操作提供的。例如 UNet 架构的 图像生成的 Diffusion Model，其像素之间关联性是卷积和 skip connection 提供的。所以即使没有 Denoise Process，GAN等生成模型也可以在类似架构上工作。

## Question
Robot action sequence 算是 continuous 还是 discrete 的？似乎是连续的，但是又没有视觉和声音一类的空间这么连续。

或者说，action space 完全可以是离散的，将视觉和声音离散化很困难，但是将 action space 离散化却似乎没什么区别。