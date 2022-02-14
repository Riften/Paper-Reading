# Brax - A Differentiable Physics Engine for Large Scale Rigid Body Simulation

## Maximal Coordinates
[Youtube: Linear-Time Variational Integrators in Maximal Coordinates](https://www.youtube.com/watch?v=kI5qBccGKfU) （好像不太对，看上去 Brax 的模拟过程并不是基于优化的。

通常，对机械臂进行参数化使用的是 minimal coordinates，每个转轴使用一个变量来描述。但是 Brax 使用的是 Maximal Coordinates:
- 每个 link 都有 6 个自由度
- 每个 link 的参考系都直接与同一个全局参考系关联，而不是互相关联。
