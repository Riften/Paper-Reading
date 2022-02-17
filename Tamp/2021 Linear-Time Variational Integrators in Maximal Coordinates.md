# Linear-Time Variational Integrators in Maximal Coordinates
- [Robotic Exploration Lab](https://roboticexplorationlab.org/)
- [Youtube: Linear-Time Variational Integrators in Maximal Coordinates](https://www.youtube.com/watch?v=kI5qBccGKfU)

一套用优化方法直接在 Maximal Coordinates 进行模拟计算的 Integrator。

**最大的问题在于由于拉格朗日力学的限制，无法直接引入摩擦等。**

这里的 Integrator 指的是物理模拟过程中的 Integrator，即已知 configure 的情况下对系统进行 forward 模拟的过程。

Maximal Coordinates
- configuration 表示为6自由度的 maximal coordinates
- constraint 表示为 dynamic equation 中的 Lagrange multiplier

## Constraint for Articulated Rigid Body
由于是在 maximal coordinates，两个 body 之间的 Joint 表述为两个 body 的位置参数的 constraint

$$g = f(x_a, q_a, p_a, x_b, q_b, p_b) = 0$$

- $x$ 位置
- $q$ 旋转
- $p$ 相对于 joint 位置

## Variational Integrator
- [Wikipedia: Principle of Least Action](https://en.wikipedia.org/wiki/Stationary-action_principle)
- [Wikipedia: Calculus of Variations](https://en.wikipedia.org/wiki/Calculus_of_variations)
- [Wikipedia: 泛函](https://zh.wikipedia.org/wiki/%E6%B3%9B%E5%87%BD)
- [Wikipedia: Lagrangian mechanics](https://en.wikipedia.org/wiki/Lagrangian_mechanics)

对于牛顿力学来说，求解一个力学系统中物体的运动轨迹，需要求解随时间变化的 constraint force，然后才能求得 $q,\dot{q}$。整个系统的运动方程基于牛顿第二定律
$$\sum F=m\frac{d^2r}{dt^2}$$

而在拉格朗日力学中，运动方程的定义基于系统的能量。定义 $L=T-V=L(q,v,t)$ 一个描述系统能量的变量，其中 $T$ 为动能，$V$ 为势能。

定义

$$\mathcal{S} = \int_{t_1}^{t_2}L(q(t), \dot{q}(t), t)dt$$

那么 Stationary-action principle 可以表述为

$$\delta \mathcal{S} = 0$$

也即能量不变。

这里的 $\mathcal{S}$ 可以看作是一个关于 $q(t)$ 的泛函，而满足 $\delta \mathcal{S} = 0$ 的解 $\tilde{q}(t)$，也就是系统的 trajectory。