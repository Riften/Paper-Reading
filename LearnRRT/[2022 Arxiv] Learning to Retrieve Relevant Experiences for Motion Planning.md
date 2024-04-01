# Learning to Retrieve Relevant Experiences for Motion Planning
FIRE paper

同作者稍早的工作:[[2019 ICRA] Using Local Experiences for Global Motion Planning](./[2019%20ICRA]%20Using%20Local%20Experiences%20for%20Global%20Motion%20Planning.md)，这里是将19年文章中的基本方法拓展到了通用环境。

核心工作是利用以往的 planning experience 来指导新的 planning (sampling)。方法是用一个新的 local primitive 来**索引** experience，通过一个 learnable 的 similarity function 来找到过去见过的相似的 local primitive，从而在 challenging region 采样。

类似的方法称为 **retrieval-based method**，把过往的 path / distribution 存到一个数据库中，然后用合适的 similarity function 找到当前 problem 相关的数据，从而辅助 planning。

## Local Primitives
首先从机械臂实际的空间位置上选几个3D坐标点，例如取URDF中每个 link 的 frame。这些坐标点被做为机械臂 configuration 的一种 projection。

$$\Pi(x)=[\pi_1(x),...,\pi_P(x)]\in \mathbb{R}^{3\times P}$$

一个 local primitive 包含两部分信息
- local workspace $lw=(b,v)$，b 是一个 4x4x4 的局部 occupancy grid，v 是这个grid 的中心位置
- configuration interleved representation $x_{PROJ} = [\lVert v-\pi_1(x)\rVert, ...,\lVert v-\pi_P(x)\rVert]\in \mathbb{R}^P$，这里的 x 可能是一次 plan 的 start/goal configuration。用该 configuration 的 P 个 projection 与 grid position 之间的距离作为该 configuration 的一种中间表示。

一个 local primitive 可以表示成
$$l = [lw, x_{\text{TARGET}}, x_{\text{PROJ}}]$$

## Critical Samples
怎么衡量 Critical? 得参考一下早先的文章。

## Experience Database
Experience Database 中存储了过往 planning 的情况，以 local primitive 为 key，以 critcal configuration 为 value。

给定 Occupancy Grid $\mathcal{W}$，request $x_{\text{START}}, x_{\text{GOAL}}$，solution $p$，FIRE通过如下算法将新的 experience 添加到数据库 $\mathcal{DB}$ 中
- Shortcut path. 使得 path 中仅仅留下 "critical samples"
- 从 start 和 goal 中选取一个 configuration 作为 target configuration 以用于 local primitive。之所以只选择其中一个，是因为 local primitive 是用来索引一个 plan experience 的，互换 start goal 应当返回同样的 experience。
- 遍历 occupancy grid，将整个 map 切分成多个 local workspace (4x4x4)
- 遍历 $p$ 中剩下的 critival samples $x$，计算其 projection $\Pi(x)=[\pi_1(x),...,\pi_P(x)]\in \mathbb{R}^{3\times P}$，对于其中的每一个坐标点 $\pi_i$，如果该坐标点位于某个 local workspace 内，则将 $\langle l : x, x^p, x^n\rangle$ 存到 $DB$ 中，这里的 $x^p, x^n$ 分别是 x 的前一个和后一个路径点。

## Similar Dataset
Similar Dataset 是用来训练 Similarity Function 的数据集。生成该数据集的核心是定义 $l$ 之间的相似度，然后将相似的 $l$ pairs 存到一个集合 $\mathcal{S}$里，其他的看作是 $\mathcal{NS}$

## Learn Similarity Function 
用 contrastive learning 的方式把 $l$ 映射到一个 latent space $z$，$z$ 之间的距离即为相似度。

$$\mathcal{L}(l_i, l_j) = \left\{
\begin{array}{l}
max(0, d_m - \lVert z_i, z_j\rVert^2) & &\text{if }\langle l_i, l_j\rangle \in \mathcal{NS}\\
\lVert z_i, z_j\rVert^2 & &\text{if } \langle l_i, l_j\rangle \in \mathcal{S}
\end{array}
\right.$$

## Inference
应用算法的时候，对于一个新的 query，先生成 $l$，然后通过 Similarity Function 找到 $DB$ 中已经存在在的 $x, x^p, x^n$ 信息，用这些信息进行 Informed Sampling。这里用了一个 Gaussian Mixture Model 来作为 Informed Sampler.

## 启发
- 用 Local Primitive 做索引，但仍然用 hyper cube 中的位置做 interpolation，减小 hash table size。
- 关注 critical samples，鼓励 generator 探索 critical samples，或者鼓励 sampler 在 critical 区域更多采样。critical 程度理论上是可以直接用 critic model parameter 来得到的。

