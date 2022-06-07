# A Survey of Scene Graph: Generation and Application
Scene Graph: 描述场景中物体、物体属性、物体之间关系的数据结构。

总结：目前从图像生成 Scene Graph 的方法有很多，但是实际用 scene graph 做 robot manipulation 的方法却很少。

Survey 主要包含两部分
- Scene Graph Generation
- Scene Graph Application

Scene Graph 基本定义
- $G = (O, E)$
- $O$ 为 objects，每个 objects 包含了他的类别标签和属性 $o_i = (c_i, A_i)$
- $E\subset O\times R\times O$ 有向边，表示物体之间的 relationships

![](../imgs/scene_graph.png)