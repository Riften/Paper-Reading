# Describing Physics For Physical Reasoning: Force-based Sequential Manipulation Planning
基于作者18年的文章 [Differentiable Physics and Stable Modes for Tool-Use and Manipulation Planning](2018%20Differentiable%20Physics%20and%20Stable%20Modes%20for%20Tool-Use%20and%20Manipulation%20Planning.md)。

求解的整个过程是一个在 Skeleton 基础上的优化过程，Skeleton 中包含了 Contact Mode 信息，决定了在哪个阶段有哪些物体之间发生了 contact 交互。

## 关于 Wrench
- [Wikipedia: Resultant force - Wrench](http://en.wikipedia.org/wiki/Resultant_force#Wrench)
- [Wikipedia: Screw Theory - Wrench](http://en.wikipedia.org/wiki/Screw_theory#Wrench)

力螺旋，偶力组。

作用在刚体上的和力可以被简化为两个部分
- 和外力：作用在简化中心的所有力的矢量和
- 力偶：所有力对简化中心的力矩的矢量和

通过合理选取简化中心，可以让力偶方向与力的方向平行，这时候这两个部分称为 wrench (力螺旋)。