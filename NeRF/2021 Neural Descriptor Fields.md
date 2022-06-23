# Neural Descriptor Fields: SE(3)-Equivariant Object Representations for Manipulation
是一个基于 demonstration 的 manipulation 方法。

隐式表达已经被证明可以编码物体的形状、视觉信息，但是这些信息并不包含 task-revelant 的信息。通常情况下的 data-driven demonstration 的方法需要大量的关于 configuration 的 demonstration，而本文希望直接把物体中与 task 有关的信息通过隐式表达来表示，从而通过 query 该隐式表达完成 task。而该隐式表达的监督信息是从 demonstration 获得的。

## Neural Point Descriptor Fields
