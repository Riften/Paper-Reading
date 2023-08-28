# The Planner Optimization Problem:Formulations and Frameworks
规范化定义了 Planner Optimization Problem，给了一个统一的 codebase。

- [home page](https://opof.kavrakilab.org/)
- [github](https://github.com/opoframework/opof)

（code很新，doc 不全，可以借鉴

## Implementation
### Generator
Generator 的输出包含了 parameter 和 entropy。

其中，网络部分输出 parameter 本身。这些 parameter 可以描述一个分布。在最简单的情况下，这个分布可以由 parameter 本身决定。

然后经过一个 sampler module 输出 entropy + parameter。也就是 reparameterization trick