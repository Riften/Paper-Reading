# Regression Planning Network
[RPN github](https://github.com/danfeiX/RPN)

核心的目的是结合两个 Task Plan 方向的长处
- 从 observation space 直接 learning-to-plan 的方法。缺少对长时间维度任务的支持。
- 传统的 Symbolic Planner，可以做到更长序列的 Task Plan。依赖于预定义的 symbolic rules and symbolic states，难以在现实场景中应用。

本文给出的网络模型相当于在 Observation Space 中做 Task Plan （而不是 Motion Plan）。而采用的方法基本可以总结为，把所有 predicates 转变成 one-hot 向量之后输入一个网络，输出结果重新解析成 predicates。

## Problem Definition
文章看待 Task Plan 问题的角度也是基于视觉的，对于传统的 Task Plan 问题而言，整个系统常被建模成一个 State-Transition System （状态机）$\Sigma = (S, A, E, \gamma)$，系统的状态由 $s\in S$ 来定义，而 $s$ 通常是由人为定义的 predicates 来声明。最终的目标形式则可以认为是一个 goalTest 过程，也可以用 predicates 来声明。PDDL 也是基于这套叙事来设计的。

但是在本文中，系统的 Observation 就是 State 本身，整个系统被看作是 $\mathcal{O} \times \mathcal{A} \times \mathcal{O} \rightarrow \mathbb{R}$，$\mathcal{O}$ 是 Observation，$\mathcal{A}$ 是 Actions。agent 原本的目的是达成 $g \in \mathcal{G}_{final}$。而在本文的基于 Observation 的叙事中，agent 的目标变成了达成一个 $o \in \mathcal{O}_g$，而这里的 $\mathcal{O}_g\subset \mathcal{O}$ 则是满足 $g$ 的 Observation。

共通点在于这里 estimate whether $o\in \mathcal{O}_g$ 的过程实际上就是一个 goalTest 过程。

## Hierarchical Policy
文中将求解 TAMP 的总体过程看作是一个 Hierarchical 的过程。

首先求解 high-level 的 policy $\mu : \mathcal{O} \times \mathcal{G} \rightarrow \mathcal{G}$，这里的输出 $g' \in \mathcal{G}$ 是一个接下来需要达到的 subgoal。

然后由 low-level 的 policy $\pi : \mathcal{O} \times \mathcal{G} \rightarrow \mathcal{A}$ 来达成每个 subgoal。

而本文值关注 high-level policy $\mu$ 的学习。

需要注意的是，这里的 high-level policy 和 low-level policy 本质上都还是 Task Plan 的范畴。所有与其说 RPN 解决了 Task Plan 问题，不如说 RPN 生成或者细化了 Task Plan 问题。

## Solution 形式
通常的 Task Plan 任务最终 Solution 的形式是 Action 的 Sequence。但是本文并不直接与 action operator 或者 symbolic state 交互。

RPN 模型的预测能力在于，根据环境的 observation，预测为了达成某个 goal 所需要首先达成的 preconditions，而不是 action operators。

本文中术语定义如下
- goal $g\in \mathcal{G}$: 一系列 predicates 组成的命题，比如 `On(pot, stove) and not IsOpen(fridge)`
- goal 中每一个 predicate 和 它的参数被称为 atom。
- goal 中的每一个 atom 被称为一个 **subgoal** $g_i$
- 一个 goal $g$ 或者 subgoal $g'$ 的 **preconditions** 可以看作另一个满足 $g$ 或者 $g'$ 之前需要首先满足的 **intermediate goal** $g' \in \mathcal{G}$。比如 `Open(fridge)` 是 `InHand(apple)` 的 precondition。

## Method
### Subgoal Serialization
本文将 goal 进行 subgoal 拆分就带来了不同 subgoal 的相容问题，执行某个特定 subgoal 的过程不能打破已经达到的其他 subgoal。本文同样采用学习的方式来达成 subgoal 的排序。

## Data 数据形式
### meta
json 格式的数据集描述，内容包括
- action_names: 可以采取的action，string list
- object_ids: 场景中的 object，integer list
- type_indices: `type_indices[i]` 是 object_id 为 `i+1` 的 object 的类型，integer list
- types: 前面 type_indices 中不同序号对应的类型名称，string list
- state_size：integer
- num_action_args：action_names 中每种 action 的参数数量，integer list
- unitary_predicates：单参数的 predicate
- binary_predicates：双参数的 predicate
- predicates：unitary_predicats + binary_predicates

### DB
实际的数据以 hdf5 group 的形式存放，其 item 包括
```
actions
dependency_trace
focus_trace
goal_mask_trace
goal_trace
gt_state
image_crops
num_goal_entities
num_object_types
object_ids
object_type_indices
reachable_trace
satisfied_trace
symbolic_state
task_id
```

## Net
网络模型使用的是 `rpn/rpn_pb.py/VBP`，总体架构如下
- ImageEncoder：卷积网络，输入RGB图片，输出feature。在整个网络中充当了从图像到状态的编码器。
  - 卷积+降采样
  - 全连接，将降采样后的图片 feature 直接作为向量输入，最终输出维度由参数指定，示例中为 32 维


## Loss
RPN 的 Loss 包含四部分
- preimage_loss
- reachable_loss
- satisfied_loss
- dependency_loss

## 问题
- goal 如何作为 RPN 输入和输出？
- 是否有 symbolic 的中间表示？
