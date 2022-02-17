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

## grad
通常用法：
```python
from jax import grad
def some_func(input0, argnums=(0,)):
    output = f(input0)
    return output
grad_func = grad(some_func)
derivative = grad_func(some_input)
```

- `some_func` 必须以 scalar 为输出
- 所有变量必须是 jax tensor，函数中间不能调用 numpy 函数，必须调用 jax.numpy 函数。一个省事的做法是把所有函数都用 jit 编译一遍。
- `grad_func` 的输入和 `some_func` 类型是一样的，但是某些情况下即使参数少于 `some_func` 也可以算出梯度。
