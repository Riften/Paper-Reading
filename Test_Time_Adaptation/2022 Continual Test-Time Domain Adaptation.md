# Continual Test-Time Domain Adaptation
任务场景是在没有 target domain 的 Ground Truth 的情况下，使用 Source Domain 的 Pre-train Model 直接在 target domain 上得到可靠结果。而且 target domain 是动态的不断变化的。

基本上是把 [Mean Teacher Method](./2017%20Mean%20teachers%20are%20better%20role%20models%3A%20%20Weight-averaged%20consistency%20targets%20improvesemi-supervised%20%20deep%20%20learning%20%20results.md) 的方法，在不同的 task setting 上跑了一遍。

## Weight-Averaged Pseudo-Labels
同 [Mean Teacher Method](./2017%20Mean%20teachers%20are%20better%20role%20models%3A%20%20Weight-averaged%20consistency%20targets%20improvesemi-supervised%20%20deep%20%20learning%20%20results.md)

## Augmentation-Averaged Pseudo-Labels
如果 **pre-train model（而不是 teacher model）** 对某个样本的置信度很低，那么就对输入加上 noise 增强之后的 teacher model 输出作为 teacher model 输出。

## Stochastic Restoration

# Implementation
[cotta github](https://github.com/qinenergy/cotta)
