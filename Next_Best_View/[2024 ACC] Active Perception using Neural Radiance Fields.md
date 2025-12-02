# Active Perception using Neural Radiance Fields

[github](https://github.com/grasp-lyrl/Active-Perception-using-Neural-Radiance-Fields)

一篇没啥用很奇怪有点垃圾，但有趣的文章。

其核心论点就在文章第一句

`We study active perception from first principles to argue that an autonomous agent performing active perception should maximize the mutual information that past observations posses about future ones.`

乍一看和 [2022 RAL Uncertainty Guided Policy for Active Robotic 3D Reconstruction using Neural Radiance Fields](./[2022%20RAL]%20Uncertainty%20Guided%20Policy%20for%20Active%20Robotic%203D%20Reconstruction%20using%20Neural%20Radiance%20Fields.md) 有点像，用 NeRF 的重建不确定性来指导 Active Perception。但是本文强调不管是 NeRF 还是什么别的途径，核心是计算过去观测和未来观测之间的互信息 Mutual Information.


对于两个随机变量 X, Y, 他们之间的互信息为

$$
I(X;Y) = H(X) - H(X|Y) 
$$

- $H(X)$, X 的熵，X的不确定性
- $H(X|Y)$, 已知 Y 之后 X 的条件熵
- 如果，XY 独立，互信息为0，知道 Y 对预测 X 没有帮助
- 如果 X Y 完全相关，互信息为 $H(X)$，即知道Y的情况下 X 的不确定性为 0，$H(X|Y) = 0$

考虑一个 active perception agent，其已经可以由过去的观测得到一个 NeRF Field $\xi$ ，其可以看作是对过去观测的一种概率性的表示，所以本文中认为 $p(y_{future}, y_{past}) = p(y_{future}|\xi)$，相应的 $I(y_{future};y_{past}) = I(y_{future};\xi)$

本文构建了多个 NeRF 模型，然后用以下值来代替 $H(y_{future})$ 和 $H(y_{future} | y_{past})$

- $p(y_{future})$ 被建模不同 NeRF 预测的分歧程度 $p(y_{future}) = \int p(\xi) p(y_{future} | \xi)$ , 该熵记为边际熵 $S(y_future)$
- $p(y_{future}|\xi)$ 被建模成单个模型的预测不确定性，其熵记为条件熵 $S(y_{future}|\xi)$

最大化上面俩值的差值，就是本文的方案。最后达到的效果通俗讲就是

“去到一个不同 NeRF 分歧很大，同时单个 NeRF 的预测准确度尽量高的视角”


| 场景类型 | 边际熵 $S(y_{future})$ | 条件熵 $E[S(y_{future}∣\xi)]$ | 互信息 I | 解释 |
|---------|-------------------------------|--------------------------------------|-------------| -- |
| 旧视角（完全已知） | 极小（≈0） | 极小（≈0） | ≈0 | 无信息增益 |
| 新视角（高度未知） | 很大（模型预测分歧大） | 较大（单模型内不确定性高） | 大（边际熵 > 条件熵） | 高信息增益潜力 |
| 完全随机观测（统计独立） | 高（但模型可能不独立） | 高（类似边际） | ≈0 | 无法改善模型 |

本文用上述方案做 Object Localization 和 Scene Reconstruction.