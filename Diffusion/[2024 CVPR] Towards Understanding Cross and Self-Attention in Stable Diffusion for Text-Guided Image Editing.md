# Towards Understanding Cross and Self-Attention in Stable Diffusion for Text-Guided Image Editing

[github](https://github.com/alibaba/EasyNLP/tree/master/diffusion/FreePromptEditing)

一篇可解释性的文章，探讨 Text-Guided Image Editing 中 attention 是怎么发挥作用的。

> the semantic meanings that these attention layers have learned and which parts of the attention maps contribute to the success ofimage editing

总体结论：
- cross-attention maps 通常包含物体属性相关的信息
- self-attention maps 对于保留原本图片的基本结构非常重要