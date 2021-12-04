# Orocos Kinematics and Dynamics Library
可以考虑使用 KDL Chain 作为当前的基本数据结构。

## Kinematic Chain
KDL 的核心类，维护了 Kinematic 信息，以及摩擦、阻尼等 Dynamic 信息。

KDL 中的 Kinematic Chain 由 Segment 组成，每个 Segment 都包含了
- 一个 Joint，Joint 所在的位置是 Segment 的 Root Frame。
- 一个 Frame，描述其 Joint 座标系和该 Segment 的 Tip Frame 之间的转换。

## ROS 中 KDL Tree 的构建
- [github: kdl_parser](https://github.com/ros/kdl_parser)

- 递归调用 `addChildrenToTree(root_link, tree)`
  - 如果 root 定义了 inertia，则读取为 `KDL::RigidBodyInertia`
  - 为 root.parent_joint 创建 `KDL::Joint`
  - 创建 `KDL::Segment`
    - name 为 root 的 name
    - jnt 为 root.parent_joint
    - frame 为 root.parent_joint.parent_to_joint_origin_transform
    - inertia 为刚刚读取的 inertia
  - 将创建的 Segment 添加到 Tree 上去
  - 遍历 root.children, 调用 `addChildrenToTree`

对于一个 Kinematic Tree，其 Root Link 被看作是固定的，因此 KDL 要求 Root Link 不能有 Inertia 信息。

很多URDF模型（例如 Franka）是不包含 Inertia 信息的。但是 URDF 一般都遵从以 Link 作为 ROOT。

当 URDF 加载到 MuJoCo 中的时候，原本的 Root link 会被作为固定的一部分，不包含 Inertia 信息，其余的 Link 要么读取 Inertia，要么计算一个 Inertia。原本的 root link 甚至不会被当作一个 link，只会有一个 geom 在 root 位置，而不会有一个 link。

## MuJoCo 中的 Kinematic Tree 信息
从 URDF 到 MuJoCo mjModel 的转换过程中，MuJoCo 将整个 URDF 组织成 Tree 的形式。而且在 MuJoCo 中，Joint 的 Frame 是放在 Link 末尾的（严格遵守 DH Model 规范），所以可以直接将各项参数读出来作为 KDL 的参数。

MuJoCo 并不把 Joint 看作是 Link 之间的连接器，MuJoCo 看待 Kinematic Tree 的方式是
- body 之间有 Tree Structure，body 之间可以理解为焊接起来的。
- 每个 body 可以有多个 joint，每个 joint 都可以以某种方式改变 body 的位置，让原本和 parent_body 焊接在一起的 body 以某种方式运动。

**MuJoCo**中 Quaternion 数组的顺序是 [w, x, y, z]

### URDF -> MJCF
URDF 中位置信息都在 Joint 中，MJCF 则在 Link 和 Joint 中都有。
- 添加 Joint 的 Parent link 作为 Body，Joint Origin 的位置和旋转作为 Body 的位置和旋转。MJCF 中 Body 的上级 Body 是谁取决于 URDF 中有没有以该 Body 为 Chilld Link 的 Joint。
- 为该 Body 增加一个 Joint，转轴维持不变，但是位置和旋转都为0，这是因为 MJCF 中 Joint 已经放在了 Body 的 Frame 上，而 Body 的位置旋转信息是从 URDF 的 Joint 中读出来的。
- 读取 URDF 中的 Child Link，以此确定下一级 Link 是谁。