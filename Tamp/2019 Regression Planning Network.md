# Regression Planning Network
[RPN github](https://github.com/danfeiX/RPN)

核心的目的是结合两个 Task Plan 方向的长处
- 从 observation space 直接 learning-to-plan 的方法。缺少对长时间维度任务的支持。
- 传统的 Symbolic Planner，可以做到更长序列的 Task Plan。依赖于预定义的 symbolic rules and symbolic states，难以在现实场景中应用。