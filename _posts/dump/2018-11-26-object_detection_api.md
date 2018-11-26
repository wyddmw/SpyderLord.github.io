---
layout: post
category: dump
title: 使用tensorflow的API直接进行训练
description: 可以说踩坑了很长时间，做一个记录
---

　　可以说到今天终于从一片混沌中走了出来，在fine-tune这部分困住了很长时间，尝试了很多不同的方法，模型也从vgg-16、AlexNet换到了最后的Mobilenet。直观上来说，感觉还是vgg16的效果确实好一些，但是我这里有点困惑的其实，因为从论文里面来看，两者的效果不应该有很大的差别，但是在实际的测试中，使用vgg-16来进行测试的时候，效果比使用mobilenet的效果要好很多，在mobilenet中没有能够成功检测出来的物体在vgg16的模型下成功实现了检测，而且准确率也很高。不清楚是不是因为对图像分辨率进行重新设置的问题，因为在使用mobilenet的时候，是使用opencv对输入的图像进行了分辨率的调整，但是这样做的后果就是人眼可见的图像失真。导致图像主体部分的物体都没有办法成功检测出来。在使用jupyter对vgg-16进行测试的时候，我其实没有仔细的看程序，不知道mobilenet效果不好的原因是不是因为图像失真导致的。其次，使用jupyter notebook进行图像的显示的时候，还是显示的是原始输入图像的分辨率，这就很神奇了。这部分的代码确实需要重新再研究研究。<br>
　　关于使用object api接口这部分的内容，直接给出链接，有问题直接查看链接就好了
[https://www.jianshu.com/p/4ec080f709d8](https://www.jianshu.com/p/4ec080f709d8)
[https://blog.csdn.net/Leon_yy/article/details/81053282]
(https://blog.csdn.net/Leon_yy/article/details/81053282)
　　