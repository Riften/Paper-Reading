# Incremental Potential Contact: Intersection- and inversion-free Large-Deformation Dynamics

## Extended-Value Action Functional
拉格朗日力学中，定义 action 泛函为

$$\mathcal{S} = \int_{t_1}^{t_2}L(q(t), \dot{q}(t), t)dt$$

这里的被积分泛函 $L(q(t), \dot{q}(t), t)$ 称为 Lagrangian。action 可以看作是一个关于函数 $q(t)$ 的泛函，$q(t)$ 同时也是 trajectory，求解系统的运动轨迹即求解泛函取极值的解 $q(t)$。在没有外力的情况下，action 极值等价于 Stationary-action principle：

$$\delta \mathcal{S} = 0$$

即能量变化值的任意微分为 0。

IPC 扩展了 action 泛函的形式，使用以下 extended-value action:

$$S(x) = \int_0^T\Large(\normalsize \frac{1}{2}\dot{x}^TM\dot{x} - \Psi(x) + x^T(f_e + f_d)\Large)\normalsize dt$$
- $x$: Mesh 顶点
- $M$： 惯性矩
- $\Psi(x)$: Hyper-elastic deformation energy 弹性形变能量，可以看作是势能的一部分
- $f_e$: 外力
- $f_d$: dissipative frictionl forces，摩擦力，会消耗能量

IPC直接计算可弹性形变的 mesh model，model 的每个顶点的位置函数 $x(t)$ 是定义域。

> x 是顶点，那 $M$ 怎么定义？

## Incremental Potential
[Variational Integrators and the Newmark Algorithm for Conservative and Dissipative Mechanical Systems](./2000%20Variational%20Integrators%20and%20the%20Newmark%20Algorithm%20for%20Conservative%20and%20Dissipative%20Mechanical%20Systems.md) 中从 Newmark Method 出发，给出了一个可行的 Integrator

$$q_{k+1} = \argmin_{q_{k+1}}\frac{1}{2 h^2}(q_{k+1}-q^{pre}_{k+1})^TM(q_{k+1}-q^{pre}_{k+1}) +\beta V_k(q_{k+1}) -\beta f_{k+1}^{ext}q_{k+1}$$

本文中也采用一样的 Euler-Lagrange Equation。
- $q^{pre}_{k+1} = q_k + h\dot{q}_{k} + \frac{h^2}{2}[(1-2\beta)\ddot{q}_{k}]$，类似的定义 $\hat{x} = x^t + hv^t + h^2M^{-1}f_e$
- $V_k$ 为势能+耗散能，等价于这里的 $\Psi(x) - x^Tf_d$

> 为什么这里直接用了 $f_e$ 而不是用求出来的上一步的 $\ddot{q}_k$？。
> 在实际的 IPC 实现中，这里直接使用了重力加速度 $g$，而不是 $\ddot{q}$。这是因为，IPC 是将所有物体看作一整个系统，动能、速度、加速度等概念都是所有物体的和。在这个基础上，contact force 和 friction 都是内力的一部分，他们的和加速度应该为 0。所以这里的 $f_e$ 仅仅包含外力，在模拟过程中仅仅包含重力。

$$\begin{aligned}
    &E(x, x^t, v^t) = \frac{1}{2}(x-\hat{x})^TM(x-\hat{x}) - h^2x^Tf_d + h^2\Psi(x)\\
    &x^{t+1} = \argmin_x E(x,x^t, v^t), ~~~~x^\tau\in \mathcal{A}
\end{aligned}$$

$x^\tau\in \mathcal{A}$ 指的是 $x^t$ 到 $x^{t+1}$ 之间的 trajectory 再 admissible trajectories 内。

要求解上面的式子，本文需要做到
- 对 Incremental Potential 进行近似。
- 正确处理摩擦带来的耗散
- 找到合适的方法处理 $x^{\tau}\in\mathcal{A}$ 这个 constraint
  - 通常的 constrained optimization 里面，通过再原本的目标函数中加上惩罚项，转化为 unconstrained optimization 问题求解，这里也一样，通过应以一个 barrier term，使其满足 1）两个表面离得远的时候为 0 2）离得太近的时候发散。

## Smoothly clamped barriers
这里的 Barrier Function 和内点法中的含义相同，将 Constrainted Optimization 问题转化成 Unconstrained Optimization 问题，要求是
- 连续
- 在 primitive 之间相距足够远的时候值为 0，这样即使通过预先计算去掉相距远的 barrier function 也不会影响最终结果
- 在 primitive 之间距离趋近于 0 的时候趋近于无穷，这也是 Barrier Function 作为 “屏障” 的意义，本文的 Barrier 条件可以保证永远不会穿模。

定义 $\hat{h}$ 为两个 primitive 之间发生 contact 的最大距离，定义 Barrier Function 为

$$\begin{aligned}b(d,\hat{d})=\left\{\begin{array}{lr}
-(d-\hat{d})^2\ln(\frac{d}{\hat{d}}), & 0 < d < \hat{d}\\
0, &d\geq \hat{d}
\end{array}\right.\end{aligned}$$

加上 $(d-\hat{d})^2$ 的目的是让 barrier 在 $h=\hat{h}$ 处二阶连续，从而可以用 Newton-type methods 求解极值。

定义 $\mathcal{C}$ 为所有不相邻的 点-面 / 边-边 primitive pair，原本最小化 IP 变成了这里的 Barrier-Augmented Incremental Potential

$$B_{t}(x) = E(x, x^t, v^t) + \kappa\sum_{k\in\mathcal{C}}b(d_k(x))\\
x^{t+1} = \argmin_{x^{t+1}} B_t(x^{t+1})$$

上式中，如果将整体看作一个 Incremental Potential，Barrier Function 在这里也充当了一个势能项，该项在两个 primitive 接触的时候为无穷大。

## Implicit Euler
最小化 $B_t(x)$ 即求解 $\nabla_x B_t(x, \hat{d})=0$，即

$$M\frac{x-\hat{x}}{h^2} = -\nabla\Psi(x) + \sum_{k\in\mathcal{C}} \lambda_k \nabla d_k(x)$$

上式形式即为一个 Newton-Euler 方程。这里的 $\lambda$ 就是等效的 contact force

$$\lambda_k = \frac{\kappa}{h^2}\frac{\partial b}{\partial d_k}$$

## Intersection-aware Line & CCD
- [Note: Optimization (Newton Method)](./Marc%20Toussaint/Optimization%2001.md)
- [DigitalRune Doc: Continuous Collision Detection](https://digitalrune.github.io/DigitalRune-Documentation/html/138fc8fe-c536-40e0-af6b-0fb7e8eb9623.htm)

基本的想法是：先用 Continuous Collision Detection 找到一个梯度方向上的最大步长，然后在做 back-tracking line search。同时为了保证 line search 期间保持 intersection free，IPC 对每一个 step size 进行一个 CCD query，如果发生碰撞，就进一步缩小 step size

### Continuous Collision Detection
和 Discret Collision Detection 对应，对于相邻的两个模拟帧，计算出两帧之间发生的 collision，而不是只在帧本身计算 collision，这有两个核心作用
- 计算出发生 collision 的时间
- 防止穿模

> 对于一些简单场景 CCD 是否是必须的？理论上来说只要 step size 的初始步长加以限制，即使离散的 Collision Detection 也能胜任大部分情形。而且会极大的提升效率。

## Friction
Incremental Potential 定义的势能项包含保守力势能和耗散势能

$$V_k(q_{k+1}) = V(q_{k+1}) + h\psi\left(q_{k+\sigma}, \frac{q_{k+1} - q_k}{h}\right)$$

这里需要做的就是正确写出摩擦导致的耗散能量 $\psi$。

### 朴素的变分形式的 Friction
最简单摩擦定律是 The Coulomb Friction Law: $F_t = \mu F_n$，这里则给出了一个摩擦力的变分形式。（凑出来的满足摩擦力定律的极值形式）

> Maximal Dissipation Principle?

摩擦力 $\beta \in \mathbb{R}^2$ 表示成以下函数极值条件

$$\argmin_\beta \beta^Tv_k ~~~~ \text{s.t.   } ||\beta||\leq \mu\lambda_k$$

- $\beta$：切向的摩擦力，由于限制了是垂直于 contact force，所以是个二维向量
- $v_k$：primitive 的切向相对速度，同样是二维向量。

这里的 $\beta^T v_k$ 是两个向量的内积。显然在 $||v_k||>0$ 时，上式结果为 $\beta = -\mu\lambda_k \frac{v_k}{||v_k||}$，即和 $v_k$ 方向相反的动摩擦力。当 $||v_k|| = 0$ 时，$\beta$ 为 contact force 切向上的一定范围的静摩擦力。因此上式是满足 Coulomb Friction Law 的。

### 参数化
Friction 的变分形式需要是一个关于 $x$ 的函数。本文中变量 $x$ 的维度根据 primitive 的类型不同可能是 $3n, n=1,2,3$。

将 Friction 表述为 $x$ 函数的核心是定义一对正交基 $T_k(x) \in \mathbb{R}^{3n\times 2}$，它将 x 的滑动位移保留垂直于 contact force 的分量

$$u_k = T_k(x)^T (x-x^t) \in \mathbb{R}^2$$
$$v_k = \frac{u_k}{h} \in \mathbb{R}^2$$

> $T_k$ 是怎么（延迟）更新的？是否可以直接求 $\nabla_x T_k$？

最后算出来的 friction force 也需要保持一样的参数维度，所以用 $T_k$ 映射回 $x$ 的空间

$$F_k(x,\lambda) = T_k(x)\argmin_\beta \beta^Tv_k ~~~~ \text{s.t.   } ||\beta||\leq \mu\lambda_k$$

但这个定义并没有找到合适的势能，而且也不是连续的，所以不能直接放在 IPC 框架使用。

### Smoothed Static Friction
- 连续性：和 barrier function 类似的处理方法，先把摩擦表达为 nonsmooth function，然后将其在一定精确度内近似为平滑函数

只要 $v_k$ 不是 0，$F_k$ 总是反向，所以首先把 $F_k$ 重新写成下面形式

$$F_k = -\mu\lambda_kT_k(x)f(||u_k||)s(u_k)$$
- s(u_k) 是摩擦力方向。当 $||u_k|| > 0$ 时，$s(u_k)$ 是 $u_k$ 方向上的2维单位向量 $s(u_k) = \frac{u_k}{||u_k||}$ 。当 $||u_k|| = 0$ 时，$s(u_k)$ 是切向上任意方向的2维单位向量。
- $f(||u_k||)$ 是摩擦力大小系数，当 $||u_k||>0$ 时为 1，否则是 $[0,1]$ 之间的值。

平滑 $f$ 的基本想法是，在位移很小的时候，使 $f$ 逐渐从 1 减小到 0.$f$ 的平滑形式是 $f_1$:

$$\begin{aligned}
  f_1(y) = \left\{\begin{array}{ll}
    -\frac{y^2}{\epsilon_v^2h^2} + \frac{2y}{\epsilon_v h},&y\in(0,h\epsilon_v)\\
    1, & y\geq h\epsilon_v
  \end{array}\right.
\end{aligned}$$
- $\epsilon_v$ 是摩擦相对速度的一个小量
- 当位移超过 $h\epsilon_v$（$h$ 是 time step，位移超过$h\epsilon_v$ 即速度超过 $\epsilon_v$）,认为发生位移，$f$ 为 1
- 当位移不到 $h\epsilon_v$ 的时候，$f$ 根据一个二次函数衰减到 0，该二次函数在 $h\epsilon_v$ 取得极大值 1，从而保证了一阶连续。
- 由于 $f$ 最终收敛到 0，所以能直接 $s(u_k) = \frac{u_k}{||u_k||}, 0$

### Dissipative Potential
- 势能定义：上述定义中用到了 contact force $\lambda$ 和正交基 $T_k$，这些都是变化量。为了避免在牛顿法更新时候计算 $\nabla_x T$，采用延迟更新 $\lambda$ 和 $T_k$ 值的方法。即先用 $\lambda_{k,t}$ 算出来 $x_{t+1}$，然后更新 $\lambda_{k,t+1}$

Incremental Potential 的原本定义中，势能项为

$$\begin{aligned}
    V_k(q_{k+1}) &= V(q_{k+1}) + h\psi\left(q_{k+\sigma}, \frac{q_{k+1} - q_k}{h}\right),\\
    q_{k+\sigma} &= (1-\sigma)q_k + \sigma q_{k+1},~~~~\sigma\in[0,1]
\end{aligned}$$

这里我们需要根据前面经过平滑的摩擦力，给出对应的耗散势能 $\psi\left(q_{k+\sigma}, \frac{q_{k+1} - q_k}{h}\right)$。

但是本文没有找到只用 $x$ 为变量的耗散势能定义方式，依然需要依赖于 $T_k(x)$ 和 $\lambda_k(x)$ 作为额外的假设已知。首先经过平滑之后的 $F$ 为

$$F_k = -\mu\lambda_kT_k(x)f_1(||u_k||)\frac{u_k}{||u_k||}$$

最直接的方法是找到函数 $T_k(x)$ 和 $\lambda_k(x)$，但是那样的话上式就不容易积分了。因此这里使用预先计算的（例如从上一个 time step 计算）$\lambda^n, T^n$ 定义 lagged friction force $F_k(x, \lambda_k^n, T_k^n)$。

有了摩擦力，本文对耗散势能的定义很简单，即摩擦力关于 $x$ 的原函数：

$$D_k(x) = \mu \lambda_k^nf_0(||u_k||)$$

- $f_0$ 为 $f_1$ 的原函数，同时满足 $f_0(\epsilon_vh) = \epsilon_vh$ (好像就是个很随意的值)

则有 $F_k(x) = -\nabla_xD_k(x)$，总的 friction potential 为 $D(x) = h^2\sum_{k\in\mathcal{C}} D_k(x)$ （？？？

### Friction 总结
Friction **大小**被近似为

$$F_k = -\mu\lambda_kf_1(x)$$
- $\mu\lambda_k$ 为滑动摩擦力
- $f_1(x)$ 是一个近似项，在相对移动很小的时候平滑的衰减到0，相对移动达到一定值的时候为1。

摩擦力的原函数被当作耗散势能

$$D_k(x) = \mu \lambda_k^nf_0(||u_k||)$$

为了方面计算梯度，$\lambda_k^n$ 被看作是定值，延迟更新。

$$
  B_t(x) = \frac{1}{2}(x-\hat{x})^TM(x-\hat{x}) - h^2x^Tf_d + h^2\Psi(x) + \kappa\sum_{k\in\mathcal{C}}b(d_k(x)) + \sum_{k\in\mathcal{C}}\mu \lambda_k^nf_0(||u_k||)\\
  x^{t+1} = \argmin_x B_t(x)
$$

## neo-Hookean material
### FEM & MPM
- [Wikipedia: Finite Element Method](https://en.wikipedia.org/wiki/Finite_element_method)

Finite Element Method (FEM) 有限单元法。

## Intersection-aware line search

## Distance Compututation
- [stackoverflow: minimum distance between two triangles](https://stackoverflow.com/questions/53602907/algorithm-to-find-minimum-distance-between-two-triangles)
- [edge segments distance](https://zalo.github.io/blog/closest-point-between-segments/)
- 有没有可能只计算点面距离？或者点点距离？

**IPC 的距离计算依赖于cache复用、条件判断，对 JAX 来说很难实现的高效。考虑使用 PQP 算法**

首先需要明确，IPC 中的 Distance 只需要保证不穿模即可。从这个角度来说，对于两个 mesh model，IPC 实际上只需要保证任意两个面片之间的距离符号不变。

计算两个面片之间的距离 $\Leftrightarrow$ 计算两个面片上距离最近的两个点之间的距离。

这两个点有两种可能
- 没有点在面片内，那么这时候实际上是计算最小的 edge-edge distance
- 有点在面片内，这时候等价于计算最小的 point-triangle 距离。


```cpp
void construct_constraint_set(
  const Candidates& candidates, // 发生碰撞的 primitive 的 index
  const Eigen::MatrixXd& V_rest,
  const Eigen::MatrixXd& V, // 顶点坐标
  const Eigen::MatrixXi& E, // Edge 列表，由 vertex index 指定
  const Eigen::MatrixXi& F, // Face 列表，同样是 vertex index 指定
  double dhat, // barrier function 参数
  Constraints& constraint_set, // return reference
  const Eigen::MatrixXi& F2E, // 每个 Face 对应的 Edge index
  double dmin // Primitive 间的最小距离？
)
```
函数的最终目的是计算 broad phrase 过程检测到的可能发生碰撞的 primitive 之间的距离，保留小于 dhat 的 primitive pair 作为 constraint。

分别处理 Edge-Vertex, Edge-Edge, Face-Vertex Candidates
- 对于 Edge-Vertex Candidates
  - 判断距离类型 point_edge_distance_type，然后针对不同的类型调用 point_edge_distance 分类计算
    - 对于点 $p$ 和边 $e(e_0, e_1)$ 计算投影和边长的比值
      $$r = \frac{(e_1-e_0)\cdot(p-e_0)}{|e|^2}$$
      - $r<0$ 说明 $p$ 离 $e_0$ 比较近，所以计算 $|p-e_0|$ 作为距离。
      - $r>1$ 说明 $p$ 离 $e_1$ 比较近，所以计算 $|p-e_1|$ 作为距离
      - 最后一种情况，距离最近的点在 $(e_0,e_1)$ 之间，计算 $d = \frac{|(e_0-p) \times (e_1-p)|^2}{|(e_1-e_0)|^2}$
  - 如果计算出的距离小于 $d^2 < (d_{min} + \hat{d})^2$ 则根据类型不同添加 constraint，前两种情况添加 vertex-vertex constraint，最后一种情况添加 vertex-edge constraint。**当前 IPC 实现中存在距离的重复计算，以 vertex-edge constraint 为例，并没有在计算后保留距离计算结果，而只保留了发生碰撞的 edge-vertex candidates，在计算能量时重复计算距离**
  - 将第三种情况 cache 到 map 中，在后续计算 edge-edge distance 时复用
- 对于 Edge-Edge Candidates 和 Face-Vertex Candidates 也是类似的实现，只是分类讨论的类型多得多。


## Implementation
关键问题：
- $\hat{x}$ 的计算，为什么定义中用的是 $f_e$ 而不用加速度？
- $\lambda, T_k$ 的更新方式？
- $M$ 用的是什么？柔性物体的 Primitive 的 Inertial Matrix 是怎么定义的？