---
layout: post
category: blog
title: Faster RCNN
description: RPNs
---

　　先给出讲解的博客链接(我发现完全靠自己看懂论文好像不太现实，怎么这么难，每次看都会有新的不明白的地方，手动捂脸.jpg)。
[https://blog.csdn.net/zhangjunbob/article/details/52622349](https://blog.csdn.net/zhangjunbob/article/details/52622349)
[https://blog.csdn.net/majinlei121/article/details/53834880](https://blog.csdn.net/majinlei121/article/details/53834880)
<br>
　　RCNN将图像提取RoI，将所有的RoI都输入到CNN中，通过SVM得到的每一个RoI的分类，第二部分将每一个RoI都做一个bounding-box的回归，得到最终的检测目标分类以及位置。<br>
　　Fast RCNN在RCNN的基础上，对整个图片作为CNN的输入，RoI在CNN得到的feature map中寻找映射到的patch，然后将SVM分类换成softmax分类器，设计了multi-task loss，将softmax分类和bounding-box回归一起进行SGD。<br>
　　Faster RCNN是在Fast RCNN的基础上做了进一步的改进，运行的速度再一次得到了提高。从整个论文来看，Faster RCNN的一个巨大的改变就是对region proposal的产生提出了新的方法，通过使用RPN网络来得到对应的候选区，不管是RCNN还是Fast RCNN，都是使用了selective search的方法去得到region proposals，但是这个方法会花费很长的时间，而且训练出来的网络并不是一个端到端的网络。Faster RCNN网络在卷积层之后加上RPN，利用RPN产生的region proposal会继续输入到ROI pooling层中，之后的步骤和Fast RCNN相同，产生分类和bounding box回归。<br>
　　在这篇论文中，作者向我们展示了一个算法上的变化，使用深度网络进行region proposals的提取，这种算法能够使在计算候选区的时候基本上没有什么花销的。我们介绍了Region Proposal Networks(RPNs)。这个网络和state-of-the-art object detection networks使用的是同一个卷积网络。<br>
　　作者在论文中提到的很重要的一点是：
- 我们发现，基于候选区的检测器使用的feature maps同样也可以用来进行候选区的提取。Our observation is that the convolutional feature maps used by region-based detectors,like Fast RCNN,can also be used for generating region proposals.On top of these features, we construct RPNs by adding two additional conv layers: one that encodes each conv map position into a short feature vector and a second that,at each conv map position,outputs an objectness score and regressed bounding for k region proposals.Our RPNs are thus a kind of fully-convolutional network and they can be trained end-to-end specifically for the task for generating detection proposals.To unify RPNs with Fast R-CNN object detection networks,we propose a simple training scheme that alternates between fine-tuning for the region porposal task and then fine-tuning for object detection,while keeping the proposals fixed.This scheme converges quickly and produces a unified network with conv features that are shared between both tasks.<br>
　　为了将RPNs和Fast RCNN网络联合起来，我们提出了一种简单的训练方式，就是在优化region proposals和优化物体检测之间交替进行。
![](/downloads/Faster RCNNalternate.png)

### Region Proposal Networks
![](/downloads/RPNs.png)
　　接下来对RPNs的网络结构进行一下整理，从上面的介绍中，可以知道的是，RPNs的输入是不限制尺寸规模大小的任意图片，然后输出的是一组rectangle object proposals，每一个候选区都会有一个物体分数。