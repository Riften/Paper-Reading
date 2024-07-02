# Using Local Experiences for Global Motion Planning
Plan 的环境非常简单，2D Robot。

方法是将整体环境分解成 local primitives，为局部环境构建自己的 sampler，然后应用到 sampling-based planner 中。

文章的一个基本假设是，环境中不同区域的 local primitives 可能导致互不相关的 challenging regions。