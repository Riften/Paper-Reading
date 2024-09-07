# A Survey on Learning from Multimodal Demonstrations
对生成式模型在 Learning from (offline) Demonstration 中应用的一个 survey.

本文对在机器人相关问题中 Learning from Demonstration 的困难总结的很到位：
- Demonstration Diversity: 对于同一个 task，不同的 demonstration 可能会有很大的区别，complex multimodal distributions。
- Heterogeneous Action and State Spaces：Condition 和 Action 的 Space 差异很大，这和 CV 问题对比时尤其明显。
- Partially Observable Demonstrations：在 human demonstration 中尤其突出，人的行为往往基于其知识和常识，这些信息时不存在于 robot observation 中的。通常的解决方法是 encode the history of observations as contexts rather than a single observation
- Temporal Dependencies and Long-Horizon Planning: 解决方法是 generate trajectories。
- Mismatch between training and evaluation objectives：训练过程的目标往往是 produce samples that resemble the training dataset，但是 evaluation metric 往往是 task success rate。
- Distribution Shifts and Generalization：distribution shift between the demonstration data and the real-world scenarios