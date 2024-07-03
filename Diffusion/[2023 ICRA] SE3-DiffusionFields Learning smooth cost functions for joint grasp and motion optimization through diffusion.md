# SE(3)-DiffusionFields: Learning smooth cost functions for joint grasp and motion optimization through diffusion
Cite 81. Jan Peters

Task: 机械臂抓取，但是并不强调解决抓取问题，而是强调通用的 optimization through diffusion 方案。

核心idea：
总体上还是设计cost function，diffusion model 输出 trajectory 同时预测 cost function，用 reverse diffusion process 优化 cost function 的思路。本文强调的是，文章构建的 Diffusion Model 是直接作用在 SE(3) 上的，为此采用了不同于矩阵表示的 SE(3) 表示。

## Denoising Score Matching
文章从 DSM 的角度把 diffusion model 的数学本质描述了一遍，关于DSM可以参考 [Ref - DSM](./Ref%20-%20Diffusion%20Model.md#Denoising%20Score%20Matching)。

## SE(3) Lie Group
