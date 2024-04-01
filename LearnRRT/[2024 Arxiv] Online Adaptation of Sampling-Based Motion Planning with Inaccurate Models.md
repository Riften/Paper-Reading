# Online Adaptation of Sampling-Based Motion Planning with Inaccurate Models

解决的核心问题是让模型在 Inaccurate 的环境中进行 Planning。

这里的 Inaccurate 是指进行完 perception 之后依然是 Inaccurate 的，比如建好 occupancy map 之后完成了 collision free 的 motion plan，但是实际执行的时候还是发生了碰撞，这时候怎么恢复 planning。
