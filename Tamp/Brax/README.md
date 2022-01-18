# Brax
- [github](https://github.com/google/brax)

Brax 使用 JAX 实现了简单地物理模拟器，从而让整个模拟过程可以求解梯度。

## 核心结构体
- `Q`: 6D Pose，位置和旋转
- `P`: `Q` 对时间的有限差分，即速度。其中旋转速度为相对质心的角速度。
- `QP`: 字面意思。QP 是 Brax 中任何 State 的表示，Brax 中 State 并不和 Configure 绑定。
