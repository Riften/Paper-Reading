# MuJoCo
- [MuJoCo Documentation](https://mujoco.readthedocs.io/en/latest/overview.html)


## Not Object-Oriented
MuJoCo 本质上是一个 C Lib，因此也决定了它不是面向对象的。这个设计理念和 OpenGL 类似，对 OpenGL 来说一切任务在于完成渲染管线，所有接口都是影响管线的，而不需要定义大量的类型。对 MuJoCo 来说，一切任务在于模拟物理世界，所有接口都是与物理环境交互。

> The reason we are not using this (Object-Oriented) here is because the dependency structure is such that the natural entity is the entire physics simulator. Instead of objects, we have a small number of data structures and a large number of functions that operate on them.

但是这并不意味着 MuJoCo 只有一个全局 context，MuJoCo 提供 mjModel 和 mjData 作为核心数据结构，mjModel 包含了所有 constant 信息，而 mjData 包含了所有 dynamic 信息。MuJoCo 接口一般使用 mjModel 和 mjData 作为其参数。