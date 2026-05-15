# TO READ
https://iconlab.negarmehr.com/POLICEd-RL/ 
https://danijar.com/project/daydreamer/
https://github.com/JiangWenPL/FisherRF
https://github.com/AlbertTan404/RoLD
[Diffusion Meets DAgger: Supercharging Eye-in-hand Imitation Learning](https://arxiv.org/html/2402.17768v1)
[MaskViT: Masked Visual Pre-Training for Video Prediction](https://arxiv.org/pdf/2206.11894)
- https://navila-bot.github.io/ NaVILA，Nvidia做得一个用 VLA 做 Navigation 的工作

https://shihao1895.github.io/MemoryVLA/ 加了 Memory 的 VLA

CTRL-WORLD: A CONTROLLABLE GENERATIVE WORLD MODEL FOR ROBOT MANIPULATION https://arxiv.org/pdf/2510.10125 可以理解和生成多视角的 World Model。

- ActiveGlasses: Learning Manipulation with Active Vision from Ego-centric Human Demonstrat. 用 Diffusion Policy 来模仿头部 camera 的轨迹
- EgoAVFlow: Robot Policy Learning with Active Vision from Human Egocentric Videos via 3D Flow. 
- https://github.com/Junyi42/LoGeR 为 VGGT 添加长程 memory。
- https://github.com/InternRobotics/G2VLM
- https://arxiv.org/pdf/2605.06270 Spark3R，在 VGGT 基础上直接做 token 压缩，从而达到20倍以上加速。

- https://www.liuisabella.com/LoHoManip/ Trace + VLA 做多步操作。
    - [Note](VLX/[2026%20Arxiv]%20Long-Horizon%20Manipulation%20via%20Trace-Conditioned%20VLA%20Planning.md)
- https://internrobotics.github.io/internvla-m1.github.io/ InternVLA-M1，(trace) conditioned vla 的比较开创性的工作
    - [2026 ICLR] ST4VLA: Spatially Guided Training for Vision-Language-Action Models 看上去 InternVLA-M1 没有中，重新投了这个
- https://www.micdz.cn/Traj2Action/ Traj2Action，同样 trace + vla，但是额外有专门的 trajectory expert 和 action expert
- https://github.com/IRMVLab/MMTwin MMTwin，预测第一人称动态视角下，人手动作轨迹
    - Novel Diffusion Models for Multimodal 3D Hand Trajectory Prediction
    - Uni-Hand: Universal Hand Motion Forecasting in Egocentric Views
- https://github.com/amap-cvlab/ABot-Manipulation ABot-Manipulation，阿里的 VLA 模型，看上去接入了 3D 信息，并且对 action space 进行了比较独特的设计。

