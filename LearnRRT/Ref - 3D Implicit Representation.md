# 3D Implicit Representation
- [Reference: Neural Implicit Representations for 3D Shapes and Scenes](https://omrikaduri.github.io/2022/06/18/Using-Neural-Implicit-Representations-for-Shape-and-Scenes.html)

Related Task
- Shape Representation, 我们关注的
- View Synthesis
- Surface Reconstruction (Image to 3D)

目前最常用的表示 3D Geometry 的 Continuous Implicit Representation 为 Signed Distance Field，以及其变体（WNF 一类）。对于我们的场景，区分 “物体内” 和 “物体外” 可能需要区分 ray-casting 中的 free cell 和 unknown cell，这需要修改一下 perception 的实现。

其他基于 NeRF 的表示通常是 encode 视觉信息而非 geometry 信息，或者需要从视觉中进一步提取 semantic 信息时使用。