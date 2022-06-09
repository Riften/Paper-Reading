# Implicit Neural Representations with Periodic Activation Functions
**SIREN Paper**

提出了用 sin 作为激活函数的网络结构，并且证明这种网络结构更适合训练 Implicit Neural Representation。

本文并不局限于特定的问题，而是期望网络能够拟合以下形式的函数

$$\mathcal{C}(\text{x}, \Phi, \nabla_{\text{x}}\Phi, \nabla_{\text{x}}^2\Phi, ...) = 0, \Phi: x \mapsto \Phi(\text{x})$$

## Formal SIREN Definition
对于输入信号 $\mathbf{x}$ 以及其观测量 $\mathbf{a(x)}$，一个经常需要被解决的问题就是拟合一个 constraint 模型 $\mathcal{C(\mathbf{x})} = 0$。例如图像补全，那么我们希望原始图像和补全图像之间的差距尽量小。例如 lagrangian physics，希望整个系统的动势增量为 0。

问题是