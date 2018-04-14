---
layout: post
category: dump
title: iteration和epoch比较
description: 
---

　　之前在一个库里面写过iteration和epoch之间的区别的，好长时间没有看，对这两个概念有点模糊，看程序的时候有的地方不是很理解，想去找之前写过的笔记，也没有找到，重新简单的整理一下：<br>
- batchsize:批次的大小。在深度学习中，通常使用SGD进行训练，也就是每次训练在训练集中取batchsize个训练样本。参数的优化过程是在进行了一次iteration之后得到的。
- iteration:1个iteration等于使用batchsize个样本训练一次
- epoch:一个epoch等于使用训练集的全部样本训练一次<br>
　　所以整个参数集进行训练的优化的次数应该就是epoch的次数乘上itearation的次数。其实想一下SGD的参数优化的方法——在进行梯度下降的时候使用的是小批次的数据集进行的，之所以提出使用SGD的方法，就是因为在之前使用整个批次进行训练的时候计算的代价太大了，所以采用这种方法减少计算量。