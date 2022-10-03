# Impedance Control
- [Wikipedia Impedance Control](https://en.wikipedia.org/wiki/Impedance_control)

## Propotional Derivative Control 比例微分控制。
- [Matthew Kelly PD Controller Demo](https://www.matthewpeterkelly.com/tutorials/pdControl/index.html)

PD Control 的目的是通过弹力和阻尼两个部分，使得机械系统能够追随某个目标点运动。

$$f=k_p(x_d - x) + k_d(v_d - v)$$

## Impedance
Mechanical Impedance 机械阻抗，机械系统针对 motion input 所输出的 force output.

Impedance 可以由几个简单的参数或者部分组成
- Spring Constant: 弹性常数，反映系统如何对 tension 张力 conpression 压力来输出力，或者说应位移输出的力。
- Damping Constant: 阻尼常数，反映系统如何对速度输入来输出力。

Impedance 反映的是机械系统如何响应环境，相对的环境如何被机械系统影响的特性则被称为 Mechanical Admittance 机械导纳。

Impedance 和 Admittance 类似电阻和电导。

## Impedance Control
对 PD Control 的一个扩展，使得 PD Control 可以在有外力的情况下运作。

考虑最简单的情形，如果把 PD Control 放到一个有恒定外力的环境中，例如重力。那么 PD Control 的稳定状态并不是到达目标点，而是会有一个偏移 $k_p\delta x = f_{ext}$。

为了考虑外力，希望有外力影响的时候，Control Force 可以自适应的抵消外力。解决方法是把加速度 $a_d$ 也作为 target。

$$f = k_p(x_d - x) + k_d(v_d - v) + m(a_d - a)$$

实际的机械系统中，外力的组成可能很复杂，会导致 Impedance Control 无法得到稳定状态（比如外力是和 configuration 有关的）。因此在上式的基础上，Impedance Control 还需要引入整个系统的 dynamic model，从而让整个系统可以得到稳定状态。

**Impedance Control 可以应对不直接依赖于 configuration 的外力，但是 Impedance Control 的系统输入并不包含外力。如果 internal dynamic model 是准确的，那么 Impedance Control 只需要运动传感器，而不需要力学传感器。**

## Joint Space Impedance Control
Joint Space 关注的是每个转轴的输入力矩 $\tau$。其基本的 dynamic equation 为

$$\tau = M(q)\ddot{q}+c(q,\dot{q}) + g(q) + h(q,\dot{q}) + \tau_{ext}$$

- $M(q)$，Initial Matrix，$M(q)\ddot{q}$ 即为维持运动所需要的力。对于一个机械系统，Initial Matrix 在不同 configuration 下是不同的，因此是 configuration $q$ 的函数（Joint Space 下也就是转轴角度）。
- $c(q,\dot{q})$: 参考系加速度带来的等效力，这里指的是科里奥利力 Coriolis 和离心力 centrifugal torque。
- $g$: 重力
- $h$: 摩擦和固有刚度（??inherent stiffness）
- $\tau_{ext}$ 其他和 configuration 无关的外力，manipulation 问题中即 contact force。

现在希望达到状态 $q_d, \dot{q}_d, \ddot{q}_d$。则一种可行的控制方程为

$$\begin{aligned}
\tau = K(q_d-q) + D(\dot{q}_d - \dot{q}) + \hat{M}(q)\ddot{q}_d+\hat{c}(q,\dot{q}) + \hat{g}(q) + \hat{h}(q,\dot{q})\end{aligned}\\
K(q_d-q) + D(\dot{q}_d - \dot{q}) + M(q)(\ddot{q}_d-\ddot{q}) = \tau_{ext}\\
Ke + D\dot{e} + M\ddot{e} = \tau_{ext}$$

KD参数这样定义主要考虑
- 上式隐含了 c，g，h 等被动力在 close loop control 的一个控制循环中保持不变，输入力的变化只需要应对 contact force 和改变加速度所需要的惯性力，而 M, c, g, h 则是根据 dynamic model 更新。这也导致单纯 Impedance Control 的效果很依赖于 dynamic model 的精确性。
- $K$ 在效果上产生随距离变大的力，类似于一个弹性系数为 K 的弹簧 spring。
- $D$ 在效果上产生随相对速度变大的力，类似于一个随速度增大的阻尼 damping。

## 总结
- Impedance Control 的效果会受到 dynamic model 精准程度的影响，而在实际环境中 dynamic model 往往是有误差或者有变化的。这也使得对于 Control (而不是 Plan) 的问题而言只有实机实验做成了才有意义。
- 单纯的 Impedance Control 并不考虑顺应性 Compliance 问题，而这可能才是我们关心的。即单纯的 Impedance Control 是一个根据外力情况输出对应驱动力的控制器，唯一的 target 为根据 trajectory 来对抗外力，并不包含更复杂的模式。
- 在此基础上让 robot control 实际 work 的 topic
    - adjustive control: 让控制器能根据环境作出应变。
    - robust control: 获得最健壮的控制策略。
    - compliance 顺应性控制。
- Controller 的 inner loop 往往是相对高频的，例如 100HZ, 500HZ。相对的，这部分算法一般要求简洁明确，可以硬件化。
