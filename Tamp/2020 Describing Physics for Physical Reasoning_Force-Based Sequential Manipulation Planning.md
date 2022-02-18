# Describing Physics For Physical Reasoning: Force-based Sequential Manipulation Planning
基于作者18年的文章 [Differentiable Physics and Stable Modes for Tool-Use and Manipulation Planning](2018%20Differentiable%20Physics%20and%20Stable%20Modes%20for%20Tool-Use%20and%20Manipulation%20Planning.md)。

求解的整个过程是一个在 Skeleton 基础上的优化过程，Skeleton 中包含了 Contact Mode 信息，决定了在哪个阶段有哪些物体之间发生了 contact 交互。在 LGP 的基础上，引入了更精确的力交互，例如交互的位置、力的大小等都可以作为优化变量参与优化。

> The core of our approach is to introduce a general parameterization of contact interactions that allows the optimizer to find wrench interactions consistent with both, contact geometry and the Newton-Euler equation.

## 关于 Wrench
- [Wikipedia: Resultant force - Wrench](http://en.wikipedia.org/wiki/Resultant_force#Wrench)
- [Wikipedia: Screw Theory - Wrench](http://en.wikipedia.org/wiki/Screw_theory#Wrench)

力螺旋，偶力组。

作用在刚体上的和力可以被简化为两个部分
- 和外力：作用在简化中心的所有力的矢量和
- 力偶：所有力对简化中心的力矩的矢量和

通过合理选取简化中心，可以让力偶方向与力的方向平行，这时候这两个部分称为 wrench (力螺旋)。

## Method
问题的构建和 LGP 总体上差不多，但是多了一些东西

### Time scaling $\tau$
在LGP框架中，duration 和 time points 是固定的，而在这里嫁了一个时间倍率 $\tau$，即优化问题中的 $t$ 并不是实际时间。当计算梯度的时候

$$\frac{dx}{d\tau}(t) = \frac{\partial x}{\partial t}(t)\frac{dt}{d\tau}(\tau(t))$$

### Point of Attack
对于 skeleton 中创建的每个 contact interaction:
- 在每一个 time step，添加一个 6D Decision Variable，包含了力大小（或者偶力组）和POA。
- 在原本的优化问题中添加新的 constraint

现实中两个物体之间的碰撞可能包含多个碰撞点，碰撞关系可能根据形状不同随时间变化。本文为了避免讨论这些进行了简化，在 CIO 中，碰撞关系被简化成了 force $\lambda$，和 eef distance $d(q)$，本文中则建模为 Point of Attack。