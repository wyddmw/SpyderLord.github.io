---
layout: post
category: blog
title: Optimization
description: parameter updates
---

## Optimization and Parameter Update
### SGD--随机梯度下降
- 随机梯度下降：在每次更新的时候使用一个样本，随机也就是说我用样本中的一个例子来近似我们所有的样本，对于最优化问题，凸问题，虽然不是每一次迭代得到的损失函数都向着全局最优化的方向，但是大的整体的方向是向着全局最优解的．
- 批量梯度下降：在每次更新的时候使用全部的样本，在更新的时候，所有的样本都有贡献，计算出来一个标准梯度。
- mini-batch梯度下降：在每次更新的时候，我们使用b个样本，使用一些较小的样本来近似代替全部的。在深度学习中，这种方法是使用最多的。