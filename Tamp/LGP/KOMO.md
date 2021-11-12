# K-Order Markov Optimization
是一种将 Path Optimization 问题转化为数学问题的模型。

## 基本概念
- Path 被表示成一个 Configuration 的序列 $T$
- 时间是离散化的
- configuration 中的 DOFs 被当作 Markov 过程中的决策变量。DOF 并不是 “速度”，而是像转轴角度这样的量。
- 声明一个 KOMO 问题首先需要确定针对哪个 Configuration，然后给出整个 KOMO 过程的 阶段 (phases) 数和每个阶段的步数，他们的乘积就是整个 Configuration Path 的长度。
- K-Order 中的 $k$ 实际上指可以优化的方程的阶数。例如要优化的 DOF 包含加速度，那么就要求 $k=2$，每次优化都牵扯到连续的 3 个Configuration。
- 优化目标称为 objectives，可以指定一系列 objectives 从而完成复杂的优化问题。

## 求解方式
RAI 实现了许多不同的接口类，例如 ConstrainedProblem，GraphProblem，KOMOProblem，这些问题会被转换成 non-linear mathematical problem。而 KOMO 提供了使用任意 non-linear mathematical problem solver 的接口。

## KOMO Tutorial
### Initialize
- 指明 Kinematic Model `KOMO::setModel`: Model 是以 Configuration 的形式给出来的。
- `KOMO::setTiming`: KOMO 中时间是离散化的，所以需要配置时间。$k$ 值的配置也在这里进行，因为 $k$ 指明了每个 objective 最多牵扯到多少个时间片的 Configuration。