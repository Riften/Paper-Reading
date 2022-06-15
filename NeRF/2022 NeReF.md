# NeRef: Neural Refractive Field for Fluid Surface Reconstruction and Implicit Representation

文章认为“学到透明物体的折射特性”等价于“学到透明物体的 surface norm”。而学得方式和 NeRF 一样，只是监督不再是用体渲染，而是用有无透明物体的 optical flow。

![](../imgs/NeRef.png)