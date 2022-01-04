# CHOMP: Gradient optimization techniques for efficient motion planning
trajectory cost 由两部分组成
$$\mathcal{U}(\xi) = f_{prior}(\xi) + f_{obs}(\xi)$$
- $f_{obs}$: obstacle term，衡量 trajectory 接近 obstacle 的程度
- $f_{prior}$: prior term，衡量 dynamical 的合理性，例如平滑度和加速度限制。

## prior term 计算
