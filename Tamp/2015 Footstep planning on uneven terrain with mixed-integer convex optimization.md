# Footstep Planning on Uneven Terrain with Mixed-Integer Convex Optimization
用 Mixed-Integer Convex Optimization 求解非平坦地面上的行走规划（指每只脚放在什么位置）问题。提出的方法叫 mixed-integer quadratically-constrained quadratic program (MIQCQP)

## 相关知识
### Mixed-Integer Optimization
既有整数也有浮点数的优化问题。是 NP-Hard 的。

## 总体方案
把避障、运动可达性、踏步旋转 整合到一个混合整数优化问题中求解。