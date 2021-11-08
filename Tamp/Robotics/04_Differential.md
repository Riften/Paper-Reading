# 微分关系
## 直线速度
对于一个向量 $Q$，它相对于座标系 $B$ 的直线速度被定义为

$$^BV_Q = \frac{d^BQ}{dt} = \lim_{\Delta t\rightarrow 0} \frac{^BQ(t+\Delta t) - ^BQ(t)}{\Delta t}$$

速度也可以在不同座标系之间转换

$$^A(^BV_Q) = \frac{^Ad^BQ}{dt} = {}^A_BR^BV_Q$$

另外，如果座标系同时也是运动的，则有

$$^AV_Q = {}^AV_{Borg} + {}^A_BR^BV_Q$$

## 旋转速度
定义 ${}^A\Omega_B$ 为座标系 B 相对于 A 的旋转速度。

由旋转引起的速度为

$${}^AV_Q = {}^A\Omega_B \times {}^AQ$$

需要注意的是，角速度方向和叉乘方向都是右手定则。

加上直线速度之后

$${}^AV_Q = {}^AV_{Borg} + {}^AR_B{}^BV_Q + {}^A\Omega_B\times({}^AR_B {}^BQ) - (*)$$

## Jacobian 矩阵
- [知乎：理解Jacobian和Hessian](https://zhuanlan.zhihu.com/p/37306749)

函数 $F:\mathbb{R}^n\rightarrow \mathbb{R}^m$ 对于 n 维向量 $\mathbf{x}=(x_1, x_2,...,x_n)^T$ 具有以下形式：

$$\begin{aligned}
    F(\mathbf{x}) = \left[\begin{array}{c}
    f_1(x_1, ..., x_n)\\
    f_2(x_1,..., x_n)\\
    .\\
    .\\
    .\\
    f_m(x_1, ..., x_n)
    \end{array}\right]
\end{aligned}$$

对于普通的单变量函数 $f(x)$，在某点 $p$ 处可以用函数的导数直线 $y = f'(p)x + b$ 近似函数。而对于这里的 $F(\mathbf(x))$，也希望能用一个仿射变换 $T(\mathbf{x}) = A\mathbf{x} + \mathbf{b}$ 来近似某点处的 $F(\mathbf{x})$。

因为 $T(\mathbf{p}) = F(\mathbf{p})$，有 $\mathbf{b} = F(\mathbf{p}) - A\mathbf{p}$，即

$$T(\mathbf{x}) = A\mathbf{x} + F(\mathbf{p}) - A\mathbf{p} = A(\mathbf{x-p}) + F(\mathbf{p})$$

而这里的仿射变换的 $A$ 就是 Jacobian 矩阵，实际上是函数 $F$ 的导数矩阵

$$\begin{aligned}J = \left[
    \begin{array}{c}
    \frac{\partial f_1}{\partial x_1}(\mathbf{p}) & \frac{\partial f_1}{\partial x_2}(\mathbf{p}) & ... & \frac{\partial f_1}{\partial x_n}(\mathbf{p}) \\
    \frac{\partial f_2}{\partial x_1}(\mathbf{p}) & \frac{\partial f_2}{\partial x_2}(\mathbf{p}) & ... & \frac{\partial f_2}{\partial x_n}(\mathbf{p}) \\
    ... \\
    \frac{\partial f_m}{\partial x_1}(\mathbf{p}) & \frac{\partial f_m}{\partial x_2}(\mathbf{p}) & ... & \frac{\partial f_m}{\partial x_n}(\mathbf{p})
    \end{array}
    \right]\end{aligned}$$

$F(\mathbf{x})$ 在 $\mathbf{p}$ 点处的近似为： $T(\mathbf{x}) = J(\mathbf{p})(\mathbf{x-p}) + F(\mathbf{p})$

## $dx=Jdq$
由 Tip Frame 座标和 World Frame 座标之间的转移关系为

$$POS=BASE*T_N*TOOL$$

POS 为世界座标系下的座标，Base 为机器人所处位置的转移矩阵，T_N 为转轴转移矩阵，TOOL 为 Tip Frame 下末端坐标。

机器人的 Configuration 设为 $q=[\theta_1, \theta_2, ..., \theta_N]^T$ （参数还可能包含 $d$），对于已知 Tip Frame 坐标的任意一点 X，它在世界座标下的位置 $X=[x,y,z,1]^T$ 可以看作由一个 $f: Q\rightarrow \mathbb{R}^3$ 得到。

求解位置可以由 $f$ 得到，速度等量则可以用微分关系来表示。

$$\frac{dX}{dt} = \frac{df(q)}{dt} = \frac{df(q)}{dq}\times \frac{dq}{dt} = J * \frac{dq}{dt}$$

上式中，$J$ 和 $J^{-1}$ 都很复杂。

### 求 $dT$
对于一个转换矩阵 T，对其在进行一个小量的转换，我们将这个增量表示为线性增加一个 $dT$。同时，这个小量的转换还代表着进行一个小量的平移和小量的旋转，所以有

$$\begin{aligned}
T+dT &= Trans(dx, dy, dz) * Rot(k, d\theta) * T\\
dT &= [Trans(dx, dy, dz)* Rot(k, d\theta) - I] * T\\
&=\Delta*T \\
&=T * {}^T\Delta
\end{aligned}$$
这里的 ${}^T\Delta = T^{-1}*\Delta*T$

$Rot(k,d\theta)$ 代表绕任意轴 $k$ 旋转 $d\theta$。

$\Delta$ 是一个 $dX=[dx, dy, dz, \delta x, \delta y, \delta z]^T$ 的函数。

$$\Delta = \left[\begin{array}{c}
    0 & -\delta z & \delta y & dx\\
    \delta z & 0 & -\delta x & dy\\
    -\delta y & \delta x & 0 & dz\\
    0 & 0 & 0 & 0
\end{array}\right]$$

如果将 $T$ 看作是机器人的转换矩阵，那就是机器人总转换矩阵的微分 $dT$，可以看作是与一个转换 $\Delta$ 相乘，$\Delta$ 可以由平移量和旋转量的微分表示。换句话说，知道末端的 6D pose 的微分，可以求转换矩阵的微分。

### 求如何通过 $dA_i$ 得到 $dT$
$$\begin{aligned}
A_i &= Rot(z, \theta_i)*Tran(0,0,d_i) * Trans(a_i, 0, 0) * Rot(x, \alpha_i)\\
&=\left[\begin{array}{c}
    \cos\theta_i & -\sin\theta_i\cos\alpha_i & \sin\theta_i\sin\alpha_i & a_i\cos\theta_i\\
    \sin\theta_i & \cos\theta_i\cos\alpha_i & -\cos\theta_i\sin\alpha_i & a_i\sin\theta_i\\
    0 & \sin\alpha_i & \cos\alpha_i & d_i\\
    0 & 0 & 0 & 1
\end{array}\right]
\end{aligned}$$

对于旋转轴，我们想要知道 $dA_i$ 和 $d\theta_i$ 之间的微分关系，而这个关系求出来很简洁

$$\begin{aligned}
\frac{dA_i}{d\theta_i} &=
\left[\begin{array}{c}
    0 & -1 & 0 & 0\\
    1 & 0 & 0 & 0\\
    0 & 0 & 0 & 0\\
    0 & 0 & 0 & 0
\end{array}\right] A_i\\
dA_i &= \left[\begin{array}{c}
    0 & -1 & 0 & 0\\
    1 & 0 & 0 & 0\\
    0 & 0 & 0 & 0\\
    0 & 0 & 0 & 0
\end{array}\right] A_id\theta_i
\end{aligned}$$

从几何意义上理解，$A_i$ 前面的矩阵代表绕 $z$ 轴旋转，乘上 $d\theta_i$ 也就是绕 $Z$ 轴旋转 $d\theta_i$，正是旋转轴本身旋转一个小量的意思，因为 D-H Model 中规定转轴方向是 $Z$ 方向。

对于平行轴，有

$$dA_i = \left[\begin{array}{c}
    0 & 0 & 0 & 0\\
    0 & 0 & 0 & 0\\
    0 & 0 & 0 & 1\\
    0 & 0 & 0 & 0
\end{array}\right] A_idd_i$$

几何意义上是因为平行轴滑动方向为 $Z$ 轴，移动 $dd_i$ 相当于沿着 $Z$ 轴移动。