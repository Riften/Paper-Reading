# ESI-BENCH: Towards Embodied Spatial Intelligence that Closes the Perception-Action Loop

- [webpage](https://esi-bench.github.io/)

Li FeiFei 组做的 Active Perception Benchmark。

## Task Definition

每个 Task 定义为 $(\mathcal{S}, p_0, q, y^*)$

- $\mathcal{S}$: 场景，来自 BEHAVIOR-1K
- $p_0$: 机器人的 initial pose
- $q$: Natural Language Question
- $y^*$: Ground Truth Answer

整个测评环境则定义为 $\mathcal{E}=\langle \mathcal{S, A, O,} T \rangle$

测评流程为，给定任务 $(\mathcal{S}, p_0, q)$，agent 在每个 step 都可以获取到 $o_t\in \mathcal{O}$，要求 agent 输出 $a_t\in \mathcal{A}$，得到 $\tau=(o_0, a_0, ..., o_t, a_t)$，在最长 30 步之内给出 answer $\hat{y}$。

Action Space 包括了 perception, locomotion, manipulation，机器人可以在场景中移动、转动视角、与物体交互，最终输出 $\text{answer}(\hat{y}, c)$，c 是 confidence。

Action Space：

![action space](../imgs/2026ESIBench.png)

任务分类：

![task categories](../imgs/2026ESIBench2.png)

## Experiments

给出了四种不同的 setup 来对算法进行测评

- **Passive Single-View**: 单帧QA，类似现有的 Spatial QA Benchmark
- **Passive Multi-View**: 给定 30 帧，不允许交互获取新的帧，类似 I-Perceive 文章使用的自建 Benchmark。
- **Active Exploration**: interaction till answer。
- **Ground-Truth Passive**: 和 Passive Multi-View 的区别在于不追求对场景的 coverage，而是实际执行任务所获取的 trajectory。

Baseline: GPT, Gemini 作为 Baseline，以及加上 VGGT 提供 3D Geometry 作为额外 baseline。