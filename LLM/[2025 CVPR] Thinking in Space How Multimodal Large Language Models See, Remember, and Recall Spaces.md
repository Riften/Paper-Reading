# Thinking in Space: How Multimodal Large Language Models See, Remember, and Recall Spaces

Jihan Yang1*, Shusheng Yang1∗, Anjali W. Gupta1∗, Rilyn Han2∗, Li Fei-Fei3, Saining Xie1

一个题目对大模型（MLLM）的空间理解和记忆能力的综合测试。

文章关心 MLLM 的以下能力
- 空间感知 perceive a space
- 空间信息的记忆 remember its layout
- 根据空间信息进行问答 retrieve this spatial information to answer questions

> Recent Multimodal LLMs can understand general videos, but can they “think spatially” when presented with a video recording of an environment? Can they build an accurate, implicit “cognitive map” that allows them to answer questions about a space? What are the strengths and limitations of using MLLMs to enhance spatial intelligence?

为了回答这个问题，本文给出了
- 一堆视频数据
- 一个 VQA Benchmark
- 测试结果

本文的最终结论很有意思
- MLLM 有一定的视觉上的空间理解能力，但不多
- spatial reasoning 能力依然是主要瓶颈
- 辅助推理的手段 （linguistic reasoning techniques）没啥效果，例如 chain-of-thought, self-consistency, tree-of-thought
- 直接给出一个 map 可以显著提升效果。