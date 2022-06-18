# Occupancy Networks: Learning 3D Reconstruction in Function Space
用 Occupancy Network 作为隐式表达。

Occupancy Function 即场景中任意座标是否是物体的一部分。

$$o:\mathbb{R}^3\rightarrow \{0,1\}$$

按照NeRF的逻辑来说，就需要一个 MLP，其输入是坐标值，输出是 $[0,1]$，每个场景/模型对应一个模型参数。本文提到了一个基本假设：一个输入 observation $x\in \mathcal{X}$，输出另一个函数 $p\in \mathbb{R}^3 \mapsto \mathbb{R}$ 的函数（泛函），等价于函数 $(p,x)\in\mathbb{R}^3\times\mathcal{X}\mapsto \mathbb{R}$。所以本文的神经网络的定以为

$$f_\theta: \mathbb{R}^3\times\mathcal{X}\rightarrow [0,1]$$