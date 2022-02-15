# JAX
- [JAX Doc](https://jax.readthedocs.io/en/latest/jax-101/01-jax-basics.html)
- [Solving Optimization Problems with JAX](https://medium.com/swlh/solving-optimization-problems-with-jax-98376508bd4f)
- [How to code Differentiation in JAX with Simple Examples](https://www.blogsaays.com/python-jax-differentiation-tutorial/)


JAX 主要目的就两个
- autograd
- accelerate

这两个目的借由三个主要的接口实现
- `grad`: 求梯度
- `jit`: 将某个函数用 XLA 编译，得到加速版本的函数
- `vmap`: 将某个函数并行运行