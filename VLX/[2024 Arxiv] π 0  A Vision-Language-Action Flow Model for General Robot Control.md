# π0: A Vision-Language-Action Flow Model for General Robot Control

Pi0 Paper

![pi0](../imgs/pi0.png)

![pi02](../imgs/pi02.png)

核心思路是用一个 Action Expert 代替纯粹的 LLM Model 来输出 Action。

## Action Expert

独立的权重，robot space action，并且使用 flow matching 训练。pi0 的一个核心发现就是 action 和 language 并不适合在一个 space 下处理，用单独的 space 和单独的权重来处理 action 可以得到好得多的效果。

pi0 的 Action Space 为

$$
\mathbf{A}_t = [a_t, a_{t+1}, ..., a_{t+H-1}],~~H=50
$$

Observation 则包含了 t 时刻的机器人 state，2~3张图片，以及一个 language instruction

$$
\mathbf{o} = [I_t^1,...,I_t^n,\mathcal{l}_t, q_t]
$$

图像和 state $q_t$ 都会经过 encoder 处理，投影到和语言一样的特征空间。

训练目标是 action 的 conditional flow matching loss

$$
L^\tau(\theta) = \mathbb{E}_p(A_t|o_t)\lVert v_\theta(A_t^\tau, o_t) - u(A_t^\tau | A_t) \rVert
$$

这里的 $\tau$ 是 flow matching 中的 continuous timestep.

Action Expert 的参数量为 300M，而用于理解图像和指令的 PaliGemma 参数量为 3B。也就是说只使用 1/10 的参数量来搭了一个 action expert。

尽管文章没有提，但看起来 action 并没有使用 latent space，而是直接在 joint angle space 下进行 flow matching。

## Non-VLM Baseline

pi0 额外搭建了一个没有 VLM 的 Baseline Model，只包含 470M 的参数。这个模型也并不是完全 from scratch 训练的，而是使用 DistilBERT 对 language 进行 encode 再处理。

这个 Baseline 在文章中讨论几乎没有。