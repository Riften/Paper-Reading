# Inductive Biases
![](../imgs/invariance_equivariance.png)

对于 CNN 来说，convolutional layers 是 shift equivariant 的，pooling layers 是 approsimately shift invariant。但是 CNN 也只是近似有这些性质，实际上为了得到更好地平移不变性、旋转不变性，有各种 trick 加到网络里。