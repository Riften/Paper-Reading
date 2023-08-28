# Instant neural graphics primitives with a multiresolution hash encoding
[github: Instant NGP](https://github.com/NVlabs/instant-ngp)

Nvidia 出的快速构建隐式表达场景的方案，几秒钟就可以完成对 NeRF 的粗略 encoding。

方案总体上来说，是除了网络参数（weight & bias）以外，对输入空间进行进一步的参数化(input encoding)，用 hash table 减小输入空间尺寸，加快收敛速度。