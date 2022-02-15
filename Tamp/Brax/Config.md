# Config
Config 是 Brax 中对于系统的描述，包含了场景的所有 static 信息以及对模拟的一些参数设定。

Config 使用 protobuf 定义，其定义文件在 `brax/physics/config.proto`

## 系统定义
- Actuator actuators: 驱动器列表，通常每个 Joint 有自己的驱动器。
  - 只支持 torque 和 angle 两种驱动器，如果要做一个可以自由移动的物体可能需要 forces
- Force forces: 外力列表，指的是不通过驱动器，可以直接施加在 body 上的力

## 模拟参数
- int32 substeps: 每次调用 `system.step` 所进行的模拟阶段数，通过一次执行多个 step 来提升系统稳定性（是重复执行模拟还是顺序执行多次？）
- float dt: 每个 step 的时长，单位 秒