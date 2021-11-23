# Basic Linear Algebra Subprograms
是一种线性计算接口的规范，简单说就是如果一个软件系统声称实现了线性计算功能，那么至少应该提供规范里的接口。

MuJoCo 自己包含了一系列 BLAS-Like 的接口，一方面在 Pipeline 中调用完成模拟，另一方面也开放这部分接口方便用户直接使用。

所以理论上，使用 MuJoCo 并不需要额外的 Eigen 之类的计算库。这些接口的列表详见 [MuJoCo API Reference: Vector Math](https://mujoco.readthedocs.io/en/latest/APIreference.html#vector-math)