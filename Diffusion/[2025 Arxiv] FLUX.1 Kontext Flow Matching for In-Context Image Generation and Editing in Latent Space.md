# FLUX.1 Kontext: Flow Matching for In-Context Image Generation and Editing in Latent Space

- [huggingface FLUX.1-dev](https://huggingface.co/black-forest-labs/FLUX.1-dev)
- [github repo flux](https://github.com/black-forest-labs/flux)

用 Flow Matching 进行图像生成的关键工作。

需要注意的是这篇文章并不是 FLUX 模型本身，而是用 FLUX 模型来做 image-to-image 生成，其突出点在于
- 没有对架构（指FLUX）进行大的修改
- 生成效果可以做非常精细的 editing，并且保持不希望 edit 的部分为原样。
- 支持多种 condition，例如用一个框框出来想要编辑的位置。


