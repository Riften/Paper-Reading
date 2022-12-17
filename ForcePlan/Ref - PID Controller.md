# PID Controller
[Wikipedia: PID Controller](https://en.wikipedia.org/wiki/PID_controller)

Propotional Integral Derivative Controller, 用 P,I,D 三项来做到 Impedance Control.

$$u(t) = K_p e(t) + K_i\int_0^te(\tau)\text{d}\tau + K_d\frac{\text{d}e(t)}{\text{d}t}$$

- $e(t)=r(t)-y(t)$，目标位置减去当前位置。
- Propotional Term: $K_pe(t)$, 即输出正比于与目标点之间的误差。
- Integral Term: $K_i\int_0^te(\tau)\text{d}\tau$, 误差在一段时间内的积分，即如果输出不能很快修正误差，那么会得到更大的输出。
- Derivative Term: $K_d\frac{\text{d}e(t)}{\text{d}t}$，相当于阻尼项。