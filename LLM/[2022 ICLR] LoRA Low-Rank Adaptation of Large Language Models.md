# LoRA: Low-Rank Adaptation of Large Language Models

LoRA Paper.

尽管 LoRA 当前已经在图像生成中广泛用于生成不同特征图像，但 LoRA 的最初提出实际上仅仅是用于 LLM 的，例如一个编程能力尤其强的模型。

Problem Statement: 给出一个自回归的 Transformer Based 语言模型 $P_{\Phi}(y|x)$，以及一个新的任务数据集 $\mathcal{Z}=\{(x_i, y_i)\}_{i=1,2,...,N}$ ，例如 $x_i$ 是自然语言， $y_i$ 是一个 SQL 指令。将原本的模型 adapte 到新的任务上。

## Core Idea

如果使用全量微调来解决上述问题，那么 learning target 大概是下面这样

$$
\max_{\Phi} \sum_{(x,y)\in\mathcal{Z}}\sum_{t=1}^{|y|} \log (P_\Phi (y_t | x, y_{<t}))
$$

假设原本的模型参数为 $\Phi_0$ ，经过微调之后变为 $\Phi_0 + \Delta \Phi$ ，那么在全量微调情况下 $|\Delta \Phi| = |\Phi_0|$ 。

而本文的核心想法就是，这里的 $\Delta \Phi$ 应当可以用另一组小得多的参数来获得 $\Delta \Phi = \Delta \Phi(\Theta)$ ，$|\Theta| \ll |\Phi_0|$ 。而本文最终找到的方案可以做到 $|\Theta|\approx 0.01\%|\Phi_0|$

## Low-Rank Adaptation

对于一个 Transformer Based 模型，他的核心参数来自于 $W_q, W_k, W_v, W_o$ 等 projection matrix，本文的一个基本假设是，adaptation 矩阵 $\Delta W$ 是低秩 low rank 的，即只需要沿着少数几个独立方向变化 $W$ ，就可以做到足够的 adaptation。

即可以把 adaptation weight 做低秩分解，对于权重矩阵 $W_0\in \mathbb{R}^{d\times k}$

$$
W_0 + \Delta W = W_0 + BA \\
B\in \mathbb{R}^{d\times r}\\
A\in\mathbb{R}^{r\times k}\\
r\ll \min(d,k)
$$

微调过程中，完全冻结 $W_0$ ，而只训练 $B,A$ 。并且在初始化微调的时候，B初始化为 0，A用 random gaussian 初始化。

