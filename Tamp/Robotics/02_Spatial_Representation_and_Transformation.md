# Chapter 2 Spatial Representation and Transformation
## Position Orientation and Frames
Position Vector 位置
$${}^AP=[P_X, P_Y, P_Z, \omega]^T$$

Orientation Matrix 方位矩阵，可以理解为是两个坐标系统之间的旋转矩阵。
$$R_A\cdot {}^AR_B = R_B$$
这里的${}^AR_B$也就是坐标系B相对于A的方位矩阵，$R_A$和$R_B$是一个单位向量在两个坐标系中的值。

而这个矩阵本身很好求
$${}^AR_B   = R_A^{T}\cdot R_B = [X_A, Y_A, Z_A]^T[X_B, Y_B, Z_B]$$

另外有特性
$${}^BR_A = {}^AR_B^T$$

如果想描述一个坐标系统相对于另一个坐标系统，则可以通过
$$\{B\}=\{ {}^AR_B, {}^AP_{B\_origin} \}$$

两个坐标系中坐标的映射关系
$${}^AP = {}^AR_B{}^BP + {}^AP_{B_{origin}}$$

用一个$4x4$矩阵（Homogenous Transformation）表示的话就是

$$\left(
    \begin{array}{l}
    {}^AP\\    
    1 
    \end{array}
\right)=
\left(
\begin{array}{l}
    {}^AR_B &{}^AP_{B\_origin} \\
    0 &1
\end{array}
\right)
\left(\begin{array}{l}
    {}^BP\\
    1
\end{array}\right)$$

- 平移具有交换性，而旋转没有。
- 多轴机器人的转换通常用的是后乘，转移矩阵在坐标后面，转移矩阵是参考前继坐标系统的相对转移矩阵，而不是参考世界坐标系的。
- 旋转矩阵的逆矩阵和转置矩阵相同，但是转移矩阵不是，${}^BT_A = {}^AT_B^{-1}$
  
$T=\left(\begin{array}{l}
    n_x & o_x & a_x &p_x\\
    n_y & o_y & a_y &p_y\\
    n_z & o_z & a_z &p_z\\
    0   & 0   & 0   & 1
    \end{array}\right)$

$T^{-1}=\left(\begin{array}{l}
    n_x & n_y & n_z &-p\cdot n\\
    o_x & o_y & o_z &-p\cdot o\\
    a_x & a_z & a_z &-p\cdot a\\
    0   & 0   & 0   & 1
    \end{array}\right)$

## MoveIt FK计算过程
FK计算过程其实就是从base开始逐级计算link坐标的过程。Moveit中通过函数`RobotState::getGlobalLinkTransform(link)`获取link在model的base坐标系中的坐标。

实际计算过程是通过`updateLinkTransformsInternal`，计算逻辑为从根到末端遍历link

$$ link.transform = parentlink.transform\cdot joint.transform$$

即当前link的transform=上级link的transform 后乘 link对应joint的转移矩阵。这里的所有transform都是相对于base frame的，所以使用后乘。

## Quaterion 四元数 & Rotation 旋转
