# Bug: IK wrong solution with invalid initial position.
**FIXED**

KDL 的 IK 求解接口为 
```cpp
int CartToJnt (const JntArray &q_init, const Frame &p_in, JntArray &q_out)
```
其中第一个参数为算法迭代的起始 configuration。如果该 configuration 是 invalid 的（比如超出 joint limit），那么会得到错误的 IK 求解结果。