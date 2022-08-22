# Mean teachers are better role models: Weight-averaged consistency targets improvesemi-supervised deep learning results

已有的提升模型范化性的方法：
- 对模型输入或者中间表示加入 noise，例如 DropOut
- $\Gamma$ model：在添加 noise 和没添加 noise 的 data 上的输出之间最小化 consistency cost。但是如果 consistency cost 占了主导，可能导致错误的结果。对此有两种优化策略：优化添加 noise 的方法，优化对 teacher model 的使用方法。本文即为后者。
- $\Pi$ model: 通过不同的 drop out 等设定，相当于在不同的 share parameter 的 model 之间最小化 consistency cost。通过这个方式，训练的时候只有很少一部分数据是 labeled 的，而对于 unlabeled 的数据就用 consistency cost。同时，consistency cost (unsupervised loss) 的权重是从 0 开始逐渐增大的，否则模型可能学不到任何东西。
- Temporal Ensembling: 和 $\Pi$ Model 类似想法，只不过是在不同 epoch 的 model 输出之间最小化 consistency cost。设定也是训练的时候只有很少一部分数据是 labeled 的，而对于 unlabeled 的数据，使用之前所有 epoch 更新得到的 pseudo label 来训练。更新方法是以往label 的 exponential moving average(EMA)。$\theta_t' = \alpha\theta_{t-1}'+(1-\alpha)\theta_t$

本文的方法和 Temporal Ensembling 非常类似，也是维护一个不断更新的以往训练的信息来辅助 unlabel 数据的训练，不同的是本文使用的是 averaging model weight 而不是 prediction。

即，pseudo label 由一个以往不同 training step 网络参数平均化的 teacher model 得到，每个当前 step 的 model 就是当前的 student model。student model 更新完之后，参数信息也会被记录下来用作之后的 teacher model 参数计算。本文称这种方法为 Mean Teacher Method。

而在更新 teacher model weights 的时候，用了 Temporal Ensembling 一样的 EMA。由于不依赖于同一个数据的 prediction，而是直接使用网络参数，所以参数更新可以每个 step 都进行，而不是一整个 epoch 结束后得到同一个数据新的 prediction 之后才能进行。对应的需要保存的历史信息也比 temporal ensembling 少多了。

**总的来说，$\Pi$ model$，Temporal Ensembling，Mean Teacher Method 都是最小化 teacher model 和 student model 之间的 consistency loss 来处理 unlabeled 数据，不同点在于 teacher model 的定义、更新方式。**
