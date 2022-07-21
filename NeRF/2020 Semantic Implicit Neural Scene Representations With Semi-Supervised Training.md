# Semantic Implicit Neural Scene Representations With Semi-Supervised Training
用 Implicit Neural Network 来表示多模态的信息，既包括形状、视觉信息，也包含 semantic 信息。

首先用一个 pre-train 的 Scene Representation Network （SRN）来从 2D Image 中提取包含 3D 信息的 Feature。