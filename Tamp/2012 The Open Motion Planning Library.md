# The Open Motion Planning library
- [官网](http://ompl.kavrakilab.org/)
- [Github](https://github.com/ompl/ompl)
- [Tutorials](https://ompl.kavrakilab.org/tutorials.html)

OMPL，对 Sample-Based Motion Planning 方法的具体实现，包括统一的接口抽象、类型和概念一致性、简单的测试 GUI 以及通用 Benchmark。

## Concepts
对于基于采样的 Motion Planner 来说，通常而言都依赖于以下概念
- State Space：agent 的 configuration 取值空间。OMPL 即可以表示自由移动的 rigid body，也可以表示多 joint 链接的多轴机器人。
- Control Space：我们不一定用得到。
- Sampler：可以是 State Sampler，也可以是 Control Sampler。
- State Validity Checker：检测 collision、limits 等。
- Local Planner：通常情况下是 State space 的插值策略。

OMPL 在设计上保留了最大限度的可扩展性，也没有特定的场景信息维护、碰撞检测、可视化等模块，同时也意味着 OMPL 自己不可以判定 state validity。保留算法可扩展性的同时，尽可能方便自己嵌入到其他软件系统中。


## Installation
```bash
apt install libompl-dev ompl-demos
```
