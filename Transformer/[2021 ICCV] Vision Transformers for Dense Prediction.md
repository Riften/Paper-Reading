# Vision Transformers for Dense Prediction

DPT Paper, Dense Prediction Transformer

用 Transformer 做 Monocular Depth Estimation

![DPT](../imgs/DPT.png)

将 CNN 换成了 Transformers，原本常用的 UNet 结构换成了专门设计的特征融合层

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