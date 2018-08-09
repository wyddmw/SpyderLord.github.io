---
layout: post
category: blog
title: DeepLearning复习(二)
description: Lecture Notes、线性分类器、softmax分类器、二分类、损失函数
---

## 学习计划
　　自动化所凉凉可能已经成为定局，接着准备其他学校和老师的面试，复习的路线是之前的note一定要重新看了，然后是之前看过的论文已经整理出来要面试之前看完的，其次是手写网络要完成了。开始着手进行了。

## Notes部分复习

### 线性分类器和SVM分类器
　　重新看线性分类器还是有点东西的。线性分类器中，我们需要两个部分，一个我们称为是得分函数scores function，另一个是损失函数，用来给每一个分类对应一个得分。假设我们有十种分类，我们的得分函数就将整个输入的图像转换成为一个对应种类数目评分的输出。权重参数矩阵w的作用可以解释为对输入的某一个空间是否感兴趣的问题。The score function can be interpreted as a function from the pixel values to class scores,which was parameterized by a set of weights W.Moreover,we saw that we don't have control over the data(xi,yi),but we do have control over these weights and we want to set them so that the predicted class scores are consistent with the ground truth labels in training data.<br>
　　接下来我们就开始说说损失函数部分。首先是SVM支持向量机的损失函数：The SVM loss is set up so that the SVM "wants" the correct class for each image to have a score higher than the incorrect classes by some fixed margin.Notice that it's sometimes helpful to anthropomorphise the loss function as we did above:The SVM wants a certain outcome in the sense that the outcome would yield a lower loss.
![](/downloads/SVM损失函数.png){:height='50' width='300'}
### softmax分类器
　　SVM是使用最多的两种分类器之一，另一个非常普遍的选择是softmax分类器，其损失函数和支持向量机是不同的。如果之前听过逻辑回归的话，softmax可以看做是逻辑回归在多分类问题中的推广。逻辑回归就是线性回归之后再加上一个sigmoid函数，将输出压缩在(0,1)之间，能够更好地表示概率。<br>
　　再重新回到softmax的分类器上来，SVM将线性输出的结果解释为每一个分类的得分，而softmax对输出的解释更加直观一些，解释为对应当前类别的概率。In the Softmax classifier,the function mapping f(xi,W)=Wxi stays unchanged,but we now interpret these scores as the unnormalized log probabilities for each class and replace the hinge loss with a cross-entropy loss.
![](/downloads/SoftmaxLoss.png){:width='1726' weight='236'}
　　上图右边括号中的式子叫做softmax函数。softmax分类器的命名就是由这个softmax函数得来的，其作用就是用来对输入的生数据进行压缩，能够使输入的各个分类的得分想家之和等于1，这样的话就能够使用交叉熵损失函数了。Softmax函数给每一个分类提供了该分类的概率，而不像SVM计算出来的是没有经过标定并且不容易解释的分数。<br>
　　在神经网络的notes中，在进行了复习之后，之前很多忽略了的知识点重新复习了一遍，目前来看，收获最大的部分是损失函数部分，对于不同的任务其实是有不同的损失函数与之对应的。

### SVM和Softmax之间的区别
![](/downloads/SVMSoftmax之间的区别.png){:height='240' weight='420'}
　　两种分类器对输出的解释不同，但是在实际的应用中，二者的表现没有非常本质上的差别。

### 二分类及其损失函数
　　重新看lecture notes还是非常有收获的。和研究生在一起做人脸识别的过程中，遇到了二分类的问题，当时一直在想，SVM的二分类损失函数怎么写，应该是和之前的多分类不一样的，想了很长时间没有想明白，后来重新看了机器学习相关的回归和SVM等内容之后，用最小二乘法的思路带进去重新又想了这个问题，感觉自己想明白了，就是一样函数的形式，求和的过程就被省略了，因为除了正确的类之外只有另外一个分类了。在notes中找到了明确的说明：
![](/downloads/BinaryLoss.png){:height="300" width="600"}
　　属性分类：当我们在完成cifar-10、MNIST数据集等的时候，我们都是假设每一个需要分类的对象都是有一个固定的标签。但是在二分类问题中，每一个分类可能并没有一个明确的属性，这个时候对应的损失函数就要做出调整。其中上面看到的二元损失函数就是其中的一种。<br>
　　另一种方案是训练一个逻辑回归分类器。一个二分类的逻辑回归分类器的输出可以理解为label是1的概率。
![](/downloads/逻辑回归.png){:width='300',hidth='100'}
　　对于逻辑回归来说，因为接了sigmoid函数，所以当sigmoid的输出大于0.5的时候，是该分类是label等于1的，反之认为对应的label是等于0的，等价来说，我们也可以认为经过线性输出之后结果如果大于零对应的label是等于1的，反之就是等于0的。此时对应的二分类逻辑回归的损失函数的表达式如下：
![](/downloads/二元逻辑回归损失函数.png){:height='70' width='451'}

### Word of caution
　　在notes中作者注明了非常重要的一点：L2范式的损失函数和SVM以及softmax等其他损失函数相比来说是更加难以优化的，这里说的L2范式的损失函数实际上就是最小二乘法的应用。所以包括L2范式的损失函数在内，softmax和SVM的损失函数可以说应该是对应了不同的分类器的，不同的损失函数用来描述不同分类器分类效果的好坏。不同的应用场景我们需要使用的分类器等也都是不一样的。感觉第二篇的notes写的还是非常不错的，已经放在github的仓库上了，给出链接[neural network2](https://github.com/SpyderLord/SpyderLord.github.io/blob/master/%E6%96%87%E6%A1%A3/Neural%20Nets%20notes%202.pdf)


## Neural Net Notes Review（1）
　　单个的神经元是可以当做线性分类器来使用的。单独的一个神经元对其输入的数据和权重矩阵进行一次点乘操作然后加上偏差。<br>
　　关于激活函数，之前总结过一次，添加一些新的内容，剩下的内容参看之前的内容就好了[激活函数总结](//spyderlord.github.io/神经网络复习)。从生物神经元的角度出发，一个神经元如果想要被激活，需要神经元受到的刺激的强度超过一定的阈值才可以。所以从建模的角度出发，激活函数的作用是对激活率的建模——sigmoid函数从不被激活到完全激活这个过程中，输出的结果从0到1，Relu函数也是根据输出的结果是比0大还是比0小来决定该神经元是否会被激活。<br>
　　神经网络的网络结构，一种非常常见的神经网络的结构是全连接层。全连接层中的神经元与其前后两层的神经元是完全成对连接的，但是在同一个全连接层内的神经元之间是没有连接的。<br>
　　还是之前已经讨论过的一个问题，神经网络的表达能力是非常强大的，神经网络能够拟合出来任何连续的函数，但是在实际中我们没有人会使用两层的一个神经网络，因为在实际的测试中，表现的情况并不理想。重新看了一下自己之前写的博客，我们使用更多层的网络，实际上是增加了网络的层次结构化。对于一个良好的识别系统来说，深度是非常重要的一个因素，因为图像是拥有层次化的结构的，所以多层的处理对这种结构是有帮助的。等复习到卷积神经网络之后会再写一些关于层次的问题。<br>
　　从网络的层数和参数的数量来分析过拟合的问题：随着神经网络层数的增加和神经元数量的增加，整个神经网络实际上表达能力是更强了，能够表达出来更加复杂的函数，可以分类更加复杂的数据。但是这样做的缺点就在于很有可能会造成过拟合。因为可能网络将数据中的噪声也拟合了进去。但是如果整个网络的深度比较浅，模型的表达能力可能就是使用非常宽泛的方式去对数据进行分类，这样做能够提高模型的泛化能力。所以说，对于不同的数据集来说，我们需要设计使用的网络的深度也是不同的。
## Neural Net Notes Review（2）
　　数据的预处理部分包括处理为以零为中心、归一化、PCA和白化etc，这些内容我们在这里不进行总结了。直接进入到参数的初始化部分。
- 从最后损失函数的结果来看，我们希望损失函数的输出尽可能小，我们进行梯度下降就是为了使损失函数不断减小，添加正则化的作用也是使权重矩阵不断减小，进而减少隐层单元的影响。那为什么我们不在初始化的时候直接讲所有的权重矩阵初始化为0呢？如果所有的权重矩阵都被初始化为0，很显然的所有的神经元的输出都将等于0，如果我们使用relu激活函数，这样的神经元是无法被激活的。除此之外，如果神经元的输出都是相同的话，在进行梯度下降的时候，所有神经元对应的梯度下降值都是相同的，这样就造成了神经元直接的不对称消失，很显然这样是不正确的。
- 补充一点关于正态分布的内容：正态分布的概率密度图像：以平均值为中心，64%的概率落在一倍标准差的范围内，95%的概率落在两倍的标准差内。所以在均值一定的条件下，标准差越大对应的图像是越胖的，反之标准差越小对应的图像越瘦。
　　