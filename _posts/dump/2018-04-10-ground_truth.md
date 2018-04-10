---
layout: post
category: dump
title: ground truth     
description:
---

## 什么是groud truth
　　在学习的时候经常会看到ground truth这个词，只是自己根据context猜测过大概什么意思，这个词到底表示什么意思一直没有查看过，这两天google了一下，和自己之前想的差不多。<br>
　　在机器学习中包括有监督学习supervised learning和无监督学习unsupervised learning还有半监督学习(semi-supervised learning)。在supervised learning中，数据是有标注的，（x,t）中x表示的是输入的数据，t表示的是标注，正确的标注是对应的ground truth。所以在做分类问题的时候，对应的ground truth应该就是对应图片正确分类的label。那么问题来了，在semantic segmentation问题中应该如何去进行ground truth的标注？如果原始图片没有进行每个像素点label的标注，应该如何进行比较？<br>
　　在深度上，应该是对应的是类别数目的通道数量。那么，这些反卷积核应该如何设置？