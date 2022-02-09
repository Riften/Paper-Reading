# System
`brax/physics/system.py`，整个 brax 系统，包含了 body，joint，actuator 等信息，以及用来与整个系统交互的接口。

## Forward Kinematics
default_qp 接口，位置 `brax/physics/system.py/System::default_qp`
- 对 config 中所有 Joint 进行排序
  - 根据 default_index 参数判断是否使用其他顺序
  - 计算 Joint 的 depth 并且按照 depth 排序
    - 把所有 joint 的 parent link 和 child link 构建成字典 lineage {child_link: parent_link}
    - 初始化 depth {link_name: depth} 为所有 link depth 都是1
        ```python
        for child, parent in lineage.items():
        depth[child] = 1
        while parent in lineage:
            parent = lineage[parent]
            depth[child] += 1
        ```
      上面的代码让每个 link 的 depth 是其前继节点的数量。
    - 对 joint 进行排序，按照 joint 的 parent link 的 depth 升序。对于第一个 joint 来说，其 parent link 并不在 depth dict 的 key 中，所以如果某个 joint 的 parent link 没有 depth 则 depth 取 0