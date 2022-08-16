# Attention
## 背景 Seq2seq 模型
![](../imgs/seq2seq.svg)

编码器是一个 RNN 的结构，而解码器也用一个 RNN 结构输出。

训练的时候，对于解码器，输入的 Y 直接是 ground truth 而不是 rnn block 的输出，H 仍然是 rnn block 输出。

测试的时候，直接把 ground truth 换成 rnn block 输出即可

## attention
attention 