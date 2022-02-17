# Brax - A Differentiable Physics Engine for Large Scale Rigid Body Simulation
论文的内容相当有限......要么是大佬们觉得“这些都是模拟器基础知识不用告诉你们”，要么是不想告诉。

## Maximal Coordinates
通常，对机械臂进行参数化使用的是 minimal coordinates，每个转轴使用一个变量来描述。但是 Brax 使用的是 Maximal Coordinates:
- 每个 link 都有 6 个自由度
- 每个 link 的参考系都直接与同一个全局参考系关联，而不是互相关联。

Maximal Coordinates 的最大优点在于其通用性，常见的 Minimal Coordinates 可以很好的建模关节体，但是当需要进行交互的时候，Maximal Coordinates 可以用同一套框架建模 contact constraints 和其他 constraints。（Brax 可能并没有完全这样做）

## Physics Loop
总体上，Brax 包含两大类结构体
- QP：maximal coordinates state 本身，是一个可以直接放进 flax network 的数据结构
- 操作和影响 QP 的各种因素的抽象
  - bodies
  - joints
  - actuators
  - colliders

整个模拟过程需要两类接口
- joint, actoators, colliders 的 apply 接口
  - 不同的类实现了不同的 constraint，apply 接口将计算这些 constraint 带来的力和力矩，返回其对 QP 的影响 $dq$
- 不同 integrator 的 apply 接口
  - 负责将 $dq$ 根据 step size $dt$ 累积到 QP 上

## Protobuf Specification
