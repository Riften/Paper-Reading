# Collider
## cull
用于筛选可能发生的 contact，只有通过筛选的 Collidable 才会参与后面的计算。

所以要想在不发生 contact 的前提下还能够得到 contact 的梯度，一个首要前提就是 cull 不能够过滤掉感兴趣的 Collidable

## contact_fn
Collider 的核心，是实际计算碰撞的时候使用的回调函数，在 Collider 的构造函数中作为参数传入。

## apply
`brax/physics/colliders.py:Collider.apply(QP) -> P`

- 获取 qp slice
- 用 `vmap` 并行执行 `contact_fn`
- 整理输出格式

