# Occupancy Networks: Learning 3D Reconstruction in Function Space
用 Occupancy Network 作为隐式表达。

Occupancy Function 即场景中任意座标是否是物体的一部分。

$$o:\mathbb{R}^3\rightarrow \{0,1\}$$

按照NeRF的逻辑来说，就需要一个 MLP，其输入是坐标值，输出是 $[0,1]$，每个场景/模型对应一个模型参数。本文提到了一个基本假设：一个输入 observation $x\in \mathcal{X}$，输出另一个函数 $p\in \mathbb{R}^3 \mapsto \mathbb{R}$ 的函数（泛函），等价于函数 $(p,x)\in\mathbb{R}^3\times\mathcal{X}\mapsto \mathbb{R}$。所以本文的神经网络的定义为

$$f_\theta: \mathbb{R}^3\times\mathcal{X}\rightarrow [0,1]$$

即神经网络输入模型数据 $x$ 和一个query的坐标点 $p$，输出 occupancy probability。网络的参数即为 $\theta$。

实际训练的时候，batch size 为 $B$，每个 batch 同时采样 K 个 query 坐标，最终 loss 为

$$\mathcal{L}_{\mathcal{B}}(\theta) = \frac{1}{\mathcal{|B|}}\sum_{i=1}^{|\mathcal{B}|}\sum_{j=1}^K\mathcal{L}(f_\theta(p_{ij}, x_i), o_{ij})$$

其中 $\mathcal{L}$ 为二分类的交叉熵。

另外，本文将整个模型分为两部分，实际估计 Occupancy Probability 的 Occypancy Network 和对不同 3D 数据的 Encoder。Encoder 将不同的 3D 数据编码到同一个 latent variable $z\in \mathbb{R}^L$。
