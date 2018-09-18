---
layout: post
category: blog
title: Faster RCNN
description: 
---

　　Faster RCNN是在Fast RCNN的基础上做了进一步的改进，运行的速度再一次得到了提高。从整个论文来看，Faster RCNN的一个巨大的改变就是对region proposal的产生提出了新的方法，通过使用RPN网络来得到对应的候选区，不管是RCNN还是Fast RCNN，都是使用了selective search的方法去得到region proposals，但是这个方法会花费很长的时间，而且训练出来的网络并不是一个端到端的网络。Faster RCNN网络在卷积层之后加上RPN，利用RPN产生的region proposal会继续输入到ROI pooling层中，之后的步骤和Fast RCNN相同，产生分类和bounding box回归。<br>
　　在这篇论文中，作者向我们展示了一个算法上的变化，使用深度网络进行region proposals的提取，这种算法能够使在计算候选区的时候基本上没有什么花销的。我们介绍了Region Proposal Networks(RPNs)。这个网络和state-of-the-art object detection networks使用的是同一个卷积网络。<br>
　　作者在论文中提到的很重要的一点是：
- 我们发现，基于候选区的检测器使用的feature maps同样也可以用来进行候选区的提取。Our observation is that the convolutional feature maps used by region-based detectors,like Fast RCNN,can also be used for generating region proposals.On top of these features, we construct RPNs by adding two additional conv layers: one that encodes each conv map position into a short feature vector and a second that,at each conv map position,outputs an objectness score and regressed bounding for k region proposals