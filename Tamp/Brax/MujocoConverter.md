# MuJoCo Converter
Brax 目前实现的 MJCF -> Brax 的方法，核心方法在 `brax/tools/mujoco.py/MujocoConverter`

- 转变的目标是从 `mjcf.MjcfElement` 到 `config_pb2,Body`，后者的定义在 `brax/physics/config.proto`。需要注意的是 MJCF 中 body 是直接构建成 Tree 的，而 brax 中 body 只是一个单个 body，由 joint 提供 constraint，类似 URDF。
- `_add_body(mjcf.MjcfElement, config_pb2.Body parent_body)`: MJCF 本质上是 body tree，以及 body 的属性。MujocoConverter 调用 `_add_body(mjcf_model.worldbody, None)`
  - 如果 parent_body 为空，说明是 world_body。否则，读取 mjcf 中 body 的 name。
  - 构建并添加 geom
    - 如果原本的 mujoco_body 没有 geom，会创建一个球形的 dummy_geom
    - mujoco_body 可以有多个 geom，该 body 的 joint 等只会放在第一个 geom 上，剩下的 geom 每一个会创建一个新的 body 并通过 fixed joint 接到第一个 body 上。
      - `GeomCollider _geom_to_collider(MjcfElement geom)`: 构建 brax 使用的 GeomCollider
        ```python
        class GeomCollider:
            """Represent a collider for a Mujoco geometry."""
            collider: config_pb2.Collider
            # Volume of the geometry in m^3.
            volume: float = 0
            # Mass of the geometry in kg.
            mass: float = 0
        ```