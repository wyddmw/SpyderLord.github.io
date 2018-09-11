---
layout: post
category: blog
title: Faster RCNN
description: 
---

　　Faster RCNN是在Fast RCNN的基础上做了进一步的改进，运行的速度再一次得到了提高。从整个论文来看，Faster RCNN的一个巨大的改变就是对region proposal的产生提出了新的方法，通过使用RPN网络来得到对应的候选区，不管是RCNN还是Fast RCNN，都是使用了selective search的方法去得到region proposals，但是这个方法会花费很长的时间，而且训练出来的网络并不是一个端到端的网络。Faster RCNN网络在卷积层之后加上RPN，利用RPN产生的region proposal会继续输入到ROI pooling层中，之后的步骤和Fast RCNN相同，产生分类和bounding box回归。