# Control
- [github: robosuite controller](https://github.com/ARISE-Initiative/robosuite/blob/master/robosuite/controllers)
- [wikipedia: Impedance_Control](https://en.wikipedia.org/wiki/Impedance_control)

## State and Control
对于一个动力（dynamic）系统，需要解决的基本问题是

```
x: state
t: time
u: control
dx/dt = f(t,x,u)
```

mujoco 中的 state 包含了以下信息
- state: `mjData.qpos`, `mjData.qvel`
- time: `mjData.time`
- control: `mjData.act`

## Data Structure
### 控制力
- mjData.ctrl：对定义好的驱动进行控制，$(nu \times 1)$，nu 是驱动数量，由模型定义。
- mjData.qfrc_applied：直接定义局部坐标系上的控制力，对于转轴来说是力矩。 $(nv \times 1)$
- mjData.xfrc_applied：定义在世界座标下的力，直接施加在 body 上。$(nbody \times 6)$

### Passive 力
- mjData.qfrac_bias：当前转轴承担的 passive 力，正常情况下就只是重力。$(nv \times 1)$


## Interface
```cpp
extern mjfGeneric mjcb_control;
```
控制使用的回调函数，修改 `mjData.ctrl`, `mjData.qfrc_applied` 和 `mjData.xfrc_applied` 等字段实现控制。

控制逻辑可以基于 位置、速度 等信息，但不能基于 contact force 等信息，或者说，由于控制回调函数在整个 pipeline 中的位置，如果控制逻辑基于 contact force，那么读取到的是上一个 step 的信息。

## mocap
用来实现动捕（motion capture）设备的输入，但也可以作为一种控制方式，充当 forward dynamics 的输入。这些动作数据被模拟器作为 constraint 处理，这也意味着以这种方式实现的控制不会直接参与动力学计算。

- `mjData.mocap_pos`
- `mjData.mocap_quat`