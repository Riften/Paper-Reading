# InteractionFusion: Real-time Reconstruction of Hand Poses and Deformable Objects in Hand-object Interactions
## 建模
本文最大的特点在于不仅仅建模了 object model （用隐式表达建模），同时还另外独立建模了 motion field。

motion field 的建模直接使用了 [DynamicFusion](./2015%20DynamicFusion.md)。这篇文章里给 DynamicFusion 中的 motion field 定义给了一个更简化的描述：

一个motion field由一系列从 canonical model $\mathcal{M}$ 表面采样的点定义，每个点有两个物理量 $\mathcal{W}_\mathcal{N}\{[p_i, T_i]\}$，分别是该点在 canonical frame 中的坐标和 transformation。

然后对于任意一点的 motion，可以对其近邻的采样点进行插值得到：

$$\mathcal{W}(\mathbf{x}) = \mathbf{SE3}(\sum_{k\in N(\mathbf{x})} \omega_k \mathbf{dq}_k)$$

- $\mathbf{dq}$ 是 dual quaternion，可以理解为 transformation 的另一种数学表达。
- $\mathbf{SE3}$ 函数将 dual quaternion 转变回 transformation matrix。
- $N(\mathbf{x})$ 即 $\mathbf{x}$ 紧邻的采样点。
- $\omega$ 是权重项，即每个采样点的权重。$\omega_k=\exp(-\lVert \mathbf{x-p_k}\rVert^2/(2\sigma^2))$

对手部的建模的大大简化，用 sphere mesh 作为 geometry，在完成标定之后将 motion 用 hand joints $\theta$ 来表示。

## 流程
最终任务是给定当前时刻 $t$ 两个深度相机的数据，给出当前重建的 static model $S$，给出前面所有时刻的 motion field $\mathcal{W}^{t*}$ 和 hand motion $\theta^{t*}$，$t* = 0,...,t-1$。输出当前时刻的 $\mathcal{W}^t$ 和 $\theta^t$，以及更新之后的 $S$。

- Segmentation: 将相机输入划分成 hand, object, background 三部分。
- LSTM Pose Prediction: LSTM 的目的仅仅是得到更准确的手部 motion，因为手部没有进行重建而是用 22 个自由度的 configuration 来表示的。LSTM 的输入是上个阶段的 22 DOF ，输出是当前状态的 22 DOF 。
  - LSTM 是单独用生成的数据训练的，数据是人为检查过没有啥遮挡、缺失一类的手部 motion 数据，另外加上 noise 得到的。
  - LSTM 仅仅是起到利用前帧信息的作用，LSTM 没有输入任何实际采集的数据，所以不能直接作为 hand motion 的结果。
- Motion Tracking: 将深度相机重建结果、LSTM 预测结果、手和物体交互的合理性结合起来得到最终的 tracking 结果 $\mathcal{W}^t$ $\theta^t$

### 最优化物体重建结果：

总体上和 [DynamicFusion](./2015%20DynamicFusion.md) 一样。