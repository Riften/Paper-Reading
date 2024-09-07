# Scaling Up and Distilling Down: Language-Guided Robot Skill Acquisition
Cite 75. Shuran Song

在 [Diffusion Policy](./[2023%20RSS]%20Diffusion%20Policy%20Visuomotor%20Policy%20Learning%20via%20Action%20Diffusion.md) 的基础上加上了 Language Conditioning.

Task: robot skill acquisition. 是一套包括数据采集声称、Diffusion Imitation Learning 的框架，Benchmark。其特点在于
- 使用 LLM 来自动化用于 imitation learning 的数据集构建过程，这个过程中会使用 motion planner 或者 grasp sampler 等工具，也会自动化标注 trajectory success / failure
- 同一个 Diffusion Policy Model 来处理不同的 language conditioning.

本文的一个很有趣的创新点在于看待 LLM 的方式，在 Code-as-Policy 或者 Say