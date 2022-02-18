# Trajectory Optimization with Contact

> Formal comparison of these various methods is difficult, as the field has not yet agreed upon a set of canonical, hard problems; this is an omportant goal for collaborative future. [Posa 01]

要想将 Contact 纳入 Optimization 框架，核心在于让 Contact 参与优化，总体上有以下方法
- Shooting Mathod：严格上不算个方法，Contact 并没有直接参与优化。但是如果 Simulator 能够对 Contact 求出关于 Trajectory 的梯度，就可以应用 Shooting Method。但是 Shooting Method 并没有 “引入 Contact” 的机制，换句话说如果当前 contact 不能满足需求，Shooting Method 并不知道如何用额外的 contact 达成任务
- 通过引入互补变量 $\phi(q)$ 来引入 Contact，把 Dynamic 表示成子优化问题。
  - 在[Mordatch 01]里，Contact Invariant Variable 充当了 $\phi(q)$ 的角色，而 Dynamics Constraints 是通过求解 Inverse Dynamics 的优化问题来引入的。 **CIO 是否满足 LCP based explicit appriches 的框架？**
  - 在[Posa 01]里，Contact 通过 LCP 求出，LCP 的基本思路是 $\lambda\phi(q)\geq 0$，需要巧妙的定义 $\phi(q)$，本质上和 CIO 是一样的。
  - 在[Toussaint 01]中，引入了 POA，把 CIO 里的 “两个发生 contact 的物体足够接近” 这一限制进一步精确到了发生 contact 的位置。
- Bi-Level Optimization [Robotic Exploration Lab 01]

对于 Brax 框架来说，在不触及 Brax 的核心实现的基础上，Brax 提供了 Contact 的计算接口 `colliders.apply`，并且这个接口是可以求梯度的 $\nabla_{q} f_{apply}$。但是
- 不可以求 $\nabla_{u} f_{apply}$，这是因为 collision 是由 configuration 造成的，而不是由 actuator 直接造成的
- 在没有发生 contact 的时候，特定 `collider` 的 $f_{apply}^i(q)=0, \forall q\in \epsilon(q_0)$，$\nabla_{q} f_{apply}^i = 0$，同样无法通过优化来引入新的 collision contact。在通过引入 $\phi(q)$ 的方法中，即使没有发生 contact， $\nabla_{\phi(q)}f_{apply}^i\neq 0$，一样可以进行优化。

如果采用流行的加入 contact 的方法，就需要在 brax 的 contact 计算中引入合理的 $\phi(q)$ 并作为优化变量参与优化。

## Brax 可能的 Reasoning 方案
本质上 contact involved optimization 需要的是求 $\nabla_{q}f_{contact}$ 的能力。而且需要在不发生 contact 的时候也可以求得梯度。

contact 的参与者可以是预定义的。

## Reference
- [Posa 01: A Direct Method for Trajectory Optimization of Rigid Bodies Through Contact][Posa 01]
- [Mordatch 01: Discovery of Complex Behaviors through Contact-Invariant Optimization][Mordatch 01]
- [Robotic Exploration Lab 01: Trajectory Optimization with Optimization-Based Dynamics][Robotic Exploration Lab 01]
- [Toussaint 01: Describing Physics For Physical Reasoning: Force-based Sequential Manipulation Planning][Toussaint 01]

[Posa 01]:./2013%20A%20Direct%20Method%20for%20Trajectory%20Optimization%20of%20Rigid%20Bodies%20Through%20Contact.md 
[Mordatch 01]:./2012%20Discovery%20of%20Complex%20Behaviors%20through%20Contact-Invariant%20Optimization.md
[Robotic Exploration Lab 01]:./2021%20Trajectory%20Optimization%20with%20Optimization-Based%20Dynamics.md
[Toussaint 01]:./2020%20Describing%20Physics%20for%20Physical%20Reasoning_Force-Based%20Sequential%20Manipulation%20Planning.md