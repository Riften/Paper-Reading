# Towards Synergistic, Generalized, and Efficient Dual-System for Robotic Manipulation

https://robodual.github.io/

RoboDual Paper.

基本想法：使用 generalist policy 和 specialist policy 两个策略系统来实现机器人的通用操作。


## Questions
1. generalist policy 和 specialist policy 的 action space 区别是怎样的？文章按照怎样的标准划分两个模型的任务？
2. 尽管 Generalist Policy 使用了不同机器人平台的数据，但最终得到的 Specialist Policy 只应用在了 ALOHA 平台，文章如何说明其方案可以广泛的扩展到不同平台？
3. 文章使用了不同架构来实现 Generalist Policy (VLA) 和 Specialist Policy (Diffusion Policy)，除了运行效率以外，是否还有其他考量？
4. 对比直接对 VLA 进行微调、使用小模型对 VLA 进行蒸馏，该文章的双模型方案的优点是什么？

------


1. What is the difference in the action space between the generalist policy and the specialist policy? What criteria is used to decide the tasks of the two models?

2. Although the Generalist Policy utilizes data from different robotic platforms, the resulting Specialist Policy is only applied to the ALOHA platform. How does the article demonstrate that their approach can be widely extended to different platforms?

3. The article employs different architectures to implement the Generalist Policy (VLA) and the Specialist Policy (Diffusion Policy). Apart from operational efficiency, are there any other considerations?

4. Compared to directly fine-tuning VLA or distilling VLA using a smaller model, what are the advantages of the dual-model approach proposed in this article?