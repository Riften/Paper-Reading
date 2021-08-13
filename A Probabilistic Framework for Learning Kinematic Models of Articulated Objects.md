# A Probabilistic Framework for Learning Kinematic Models of Articulated Objects
主要是介绍对关节体的建模方法，怎么对关节体进行参数化的描述。

使用的描述方式是 Kinematic Graphs
- 顶点代表物体的不同部分
- 顶点之间的边代表各部分之间的运动关系（Kinematic Relationship）

建模方法的核心在于定义Kinematic Graphs中不同类型的边的参数形式。

除了建模方法本身，文章还包括了对建模方法的几方面使用
- 如何估计运动模型
- 如何使用运动模型来进行姿态预测，以及机器人任务的构建

## Introduction