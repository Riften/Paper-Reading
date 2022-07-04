# Modeling, learning, perception, and control methods for deformable object manipulation
## Physics Based Modeling
## Perception
Perception 的最核心问题是对柔性物体的 state 有一个合适的定义。如果已经有了合适的定义，那么 state estimation 问题可以表述为优化问题

$$
\begin{aligned}
x^* &= \argmin\lVert o-\mathbf{M}(x)\rVert\\
x&\in\text{ObjectStates}    
\end{aligned}
$$

![](../imgs/deformable_state_representation.png)

### Template-based
以已有的数据，例如图片、模型数据为模板，来表示当前某个变形物体的状态。

为了实现与 template 的匹配，可以计算 vertex 之间的距离一类的。