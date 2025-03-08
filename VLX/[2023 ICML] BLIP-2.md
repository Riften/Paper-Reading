# BLIP-2: Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models
Salesforce Research. Junnan Li. Steven Hoi.
[github](https://github.com/salesforce/LAVIS/tree/main/projects/blip2)

Task: 在 frozen Image Encoder 和 LLM 之间构建起统一的 embedding。

General Idea: 用一个 Trainable Query 来提取 “visual representation most relevant to the text.”

## Q-Former
