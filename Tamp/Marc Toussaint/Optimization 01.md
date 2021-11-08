# Optimization
优化问题的通用形式

$x\in \mathbb{R}^n$, $f: \mathbb{R}\rightarrow\mathbb{R}$, $g: \mathbb{R}^n\rightarrow \mathbb{R}^m$, $h: \mathbb{R}^n\rightarrow \mathbb{R}^l$ Find

$$\begin{aligned}
    \min_x~~~&  f(x) \\ 
    \text{s.t.}~~~&  g(x) \leq 0, h(x) = 0
\end{aligned}$$

- f(x) 的不同性质决定了优化的求解方式，主要是 f(x) 的可微性。
- 可以通过 $f(x)$ 的采样计算 $\nabla f(x)$ 的局部值
- 可以通过 $\nabla f(x)$ 的采样计算 $\nabla^2f(x)$ 的局部值

## Unconstraint Optimization
最基本的基于梯度下降的优化
- Gradient Vector: $\nabla f(x) = \left[ \frac{\partial}{\partial_x}f(x) \right]^T$
- 每次根据 stepsize $\alpha$ 更新 $x$ 值： $x \leftarrow x - \alpha \nabla f(x)$
- 直到变化小于 tolerance $\theta$: $|\Delta x| < \theta$

### Stepsize 适应
简单说就是希望