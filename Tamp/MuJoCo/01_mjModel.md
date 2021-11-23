# mjModel
`mjModel` 是 MuJoCo 存储所有 constant 信息的结构，它维护了物理世界中所有 joint、body、shape 等信息。

## 查询接口
- `const char * mj_id2name(mjModel, type, id)`: 获取某个 Joint 的名称。不管是对于物理模拟还是 XML 脚本来说，名称都不是必须的。第二个参数是一个 `int`，而其实际取值在 `mjmodel.h/mjtObj`，也就是说虽然定义了 `enum` 类型，接口实际使用的还是 `int`。不太明白这种有效降低代码可读性的设计是为了啥。
- `int mj_name2id(const mjModel* m, int type, const char* name)`: 上面的接口反过来。
- `int * mjModel::jnt_qposadr`: 是一个 index 的数组，`jnt_qposadr[joint_id]` 特定 joint 在 qpos 数组中的 start index
  - 查询某个名字的 joint 的 pos: `mjData.qpos[mjModel.jntqposadr[mj_name2id(mjModel, type, joint_name)]]`
- `int * mjModel::jnt_dofadr`：和上面类似，是在 qvel 数组中的 start index


## 刚体的 Bodies, geoms, sites
Bodies 定义的是刚体的物理性质，包括质量和惯性矩（inertia matrix）。Bodies 同时充当了 Geoms 和 Sites 的容器。

Geoms 是附加在 Body 上的形状信息，可以是 primitive geometric shape 也可以是 mesh 或者 height field。主要作用包括
- 计算 collision
- 计算 contact force
- 在惯性矩未定义的时候自动推断惯性矩，默认情况下假设密度和水一样，且均匀分布。
- 可视化。

Site 用来定义一些其他的属性，例如传感器、曲柄端点、肌腱之类的，或者用来指定一些用户关心的点的位置。这些部分并不参与碰撞计算。