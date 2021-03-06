# Logic-Geometric Programming
- [Robotics Course](https://marctoussaint.github.io/robotics-course/simlab.html): 看上去是 Marc Toussaint 为 lab 快速上手 RAI 相关 Code 所写的文档。
- [Github: RAI maintenance](https://github.com/MarcToussaint/rai-maintenance)： 编译 RAI 需要的一些辅助脚本，以及一些简单的概念介绍文档。

## Install
Ubuntu 可以直接通过 `make -j1 installUbuntuAll` 安装依赖，但是缺少了 GLFW3
```
sudo apt-get install libglfw3 libglfw3-dev
```

另外部分库使用了 bullet，而原仓库没有把 bullet 直接包括进去，要么安装 bullet，要么使用 submodule。这里直接安装 bullet 然后在根目录 CMakeLists.txt 中添加 `find_package(Bullet Required)`

## 总体架构


### Configuration
LGP 用于维护场景信息的数据结构，定义在 `rai/Kin/kin.h`。包含了场景中的物体信息和物体之间的链接关系。

Configuration 包含的信息包括一系列 Frame（坐标系），每个 Frame 又有自己的 Parent Frame。并且进一步维护了 Frame 的以下信息
- dof：自由度。
- shape
- inertia
- 部分情况下还包括 object 之间的 contact 以及其 dof

### Features
关联 Configuration 和数值计算的 interface，任何从 Configuration 中计算的值都可以看作一个 Feature。常见的 Feature 例如
- 基本的场景信息，例如物体位置，shape 之间的距离，两个物体之间距离最近的点。
- 复杂一点的场景信息，例如 configuration 的 total energy。
- 误差或残差，例如物体当前位置和期望位置之间的差距。

### Degrees of Freedom (dofs)
LGP 将 dofs 定义的更加广泛，所有数学求解过程中的决策变量都被看作了 configuration 中的 dofs，例如物体之间的力交互情况。

### 速度表示
LGP 中速度是由两个 Configuration 差异来表示的，这也顺应了优化系统对时间离散化的需求。为了以优化的方式求解控制问题，通常需要三个 Configuration，$t$ 时刻和 $t-\tau$ 时刻的 Configuration 代表了当前 State。而 $t+\tau$ 时刻的 Configuration 则是优化目标。

## Mathematical Problem 的构建
Mathematical Problem 的接口定义在 `rai/Optim/MathematicalProgram.h`，这是一个纯虚类，所有实际的问题需要继承这个类。

### Conv_KOMO_SparseNonfactored

## Optimizer


## LGP 的局限性
- 所有 kinematic, model, task 等信息都通过 `.g` 文件以 graph 的形式描述，粗暴，同时也难以和其他系统共用，相对闭源。

