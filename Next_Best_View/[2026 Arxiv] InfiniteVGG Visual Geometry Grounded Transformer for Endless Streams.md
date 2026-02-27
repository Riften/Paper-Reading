# InfiniteVGGT: Visual Geometry Grounded Transformer for Endless Streams

Shuai Yuan,1   Yantai Yang,1, 2   Xiaotian Yang,1   Xupeng Zhang,1  
Zhonghao Zhao,1   Lingming Zhang,   Zhipeng Zhang1 ✉  

1AutoLab, School of Artificial Intelligence, Shanghai Jiao Tong University  
2Anyverse Dynamics

本文是在 StreamVGGT 的基础上加上了一些 Memory Trick，使其支持无限长的图像流。

在 VGGT 的基础上加上了 Memory 机制，使得 VGGT 可以被流式、增量的调用，且支持无限长的图像流。

算法的核心是一个 Rolling KV Cache Memory。

## Methodology

在 [StreamVGGT](./[2025%20Arxiv]%20Streaming%204D%20Visual%20Geometry%20Transformer.md) 的基础上优化 memory 部分。