# Active Articulation Model Estimation through Interactive Perception
## Introduction
做的事情主要是直接对关心的关节体模型本身应用Interactive Perception，从而降低关节体运动对于机器人来说的不确定性。

**Note**: 文章不强调关节体分类，但是在最终结果上还是做了一个运动模型的分类。

**Note:**: 文中提到的关节体模型（Articulation Model）指的是关节体的运动模型（Kinematic Model）。

传统的方法是基于拟合的，即通过被动的数据输入，将关节体模型拟合到最接近的关节体模型中。基于拟合的方法存在的问题包括
- 没法充分说明拟合的可靠性，比如当前的拟合模型是不是就是数据最合适的解释。
- 只关注对数据的拟合程度，而不对实际的物理约束情况进行推测，在应用场景上存在很大局限性。
- 机器没办法进一步得知如何获得更多需要的数据信息。

本文的方法是构建和追踪关节体运动模型的分布（包含模型类型及其关键参数等），并且根据操作的输出更新模型分布。同时，机械臂也将选择能将关节体分布快速收敛的行为。

Contribution
- particle filter approach（？）
- 根据机械臂反馈来更新模型似然估计的模型
- 基于概率的行为选择算法，目的是为了更快的降低关节体模型的不确定性（我的理解里这里的不确定性是其物理限制形式的不确定性，例如是绕轴的，滑动的，之类）

## Related Work

## Approach
### Articulation Model Representation
使用的数据主要有两种
- 基于视觉信息的物体追踪数据
- 机械臂行为的结果（挪不挪得动一类）

**Articulation Models and Their Parameters**: 文中只考虑四类关节体
- 固定物体 rigid
- 可定向滑动物体 prismatic
- 可旋转物体 rotational
- 自由物体 free-body
