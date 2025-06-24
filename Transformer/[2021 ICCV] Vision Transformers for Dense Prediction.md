# Vision Transformers for Dense Prediction

DPT Paper, Dense Prediction Transformer

用 Transformer 做 Monocular Depth Estimation

![DPT](../imgs/DPT.png)

将 CNN 换成了 Transformers，原本常用的 UNet 结构换成了特征融合层

```
ViT Features:  [layer_1] [layer_2] [layer_3] [layer_4]
                  ↑        ↑        ↑        ↑
               细节特征  中层特征  高层特征  语义特征

Fusion Flow:
layer_4 → refinenet4 → path_4
                        ↓
path_4 + layer_3 → refinenet3 → path_3  
                                  ↓
                path_3 + layer_2 → refinenet2 → path_2
                                                  ↓
                               path_2 + layer_1 → refinenet1 → final output
```

(上面的图是 Claude Sonnet 4 画得)

特征融合层则是上采样插值和卷积的组合，将 patch 还原成像素尺度。

通俗来理解， DPT 的做法很像是将 ViT 和 CNN 之间关系的 “刻板印象” 用网络结构实现了出来。ViT 的提出就伴随着对 Attention 和 CNN 之间运算关系的讨论，CNN可以看作是一种固定 attention score 的局部 attention，要用 CNN 做 dense prediction 往往需要一个 UNet-like 的结构，因为只有这样才能在 bottle neck 部分得到局部的特征。而 attention 用更大的计算量灵活处理空间位置（patch）之间的关系。

在 DPT 的设计中，保留了 self-attention 对全局建模的能力，同时在空间细节的处理上利用了 CNN 稳定的局部特征处理能力。