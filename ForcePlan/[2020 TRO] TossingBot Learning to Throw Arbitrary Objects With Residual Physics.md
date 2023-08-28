# TossingBot: Learning to Throw Arbitrary Objects With Residual Physics
<u>Andy Zeng , Shuran Song</u>

TRO Best Paper

扔的物体的任意形状的，会联合优化抓取和扔的动作。而学习的输入仍然是视觉信息。

总体上是 RL 的框架，成功扔到目标 box 里面算是 accurate throw。然后学习从 visual observation 到抓取和扔的参数（likelihood）的 mapping。

一个核心的 trick 是扔的参数是一个 residual action，先用扔一个理想的球体计算一个 $\hat{v}$ ，模型则输出残项 $\delta$，俩加起来作为最终的 prediction $v=\hat{v}+\delta$。这个思路和之前的甩绳子的工作很类似（TODO：贴一下这一篇）。但是这也使得，实际上**最重要的学习内容反而是抓取姿态**，只要能稳稳地扔出去，和扔一个球也差不了多少。*We observe that throwing performance strongly correlates with the quality of grasps.*

