---
layout: post
category: blog
title: Optimization
description: parameter updates，重新梳理一下最优化的过程
---

## Optimization and Parameter Update

### 损失函数重新复习
　　损失函数的作用是用来进行量化某个具体权重集W的质量，我们现在使用到的损失函数主要是softmax函数，通过交叉熵来评估模型的好坏与否。复习一下softmax函数的用法，每一次看之前看过的内容都会有新的收获。先给出wiki上关于softmax函数的百科链接[softmax](https://zh.wikipedia.org/wiki/Softmax%E5%87%BD%E6%95%B0)。在数学，尤其是概率论和相关理论中，softmax函数或是称为归一化指数函数，是逻辑函数的一种推广。
![](/downloads/softmax.png)
　　softmax函数实际上是有限项离散概率分布的梯度的对数的归一化，放在我们的分类问题中，我们可以认为得到的是某一个输入在多个类别中对应的分类。得到了概率值之后再进行熵的计算，简单来说就是直接计算上述求得的概率值的自然对数值。<br>
　　想清楚了这点，我觉得其实当我们再看到loss的时候就可以想出来对应的分类的概率是多少。如果我们的正确的概率是0.5，计算得到的loss的结果就应该是0.7左右，所以除非是svm形式的损失函数，softmax计算出来的损失值不会特别大，如果特别大说明对应的概率是非常小的。
#### 所以再次印证了我现在越来越重视的一个观点，理论基础一定要打扎实了，遇到了问题将问题和理论结合起来，而不是凭空去猜想。
### 复习optimization的流程
　　因为在tensorflow上没有反向传播的过程，还是以之前的softmax为示例重新去回顾一下整个optimization的过程是什么样的。
- 

```python
def softmax_loss_naive(W, X, y, reg):
  """
  Softmax loss function, naive implementation (with loops)

  Inputs have dimension D, there are C classes, and we operate on minibatches
  of N examples.

  Inputs:
  - W: A numpy array of shape (D, C) containing weights.
  - X: A numpy array of shape (N, D) containing a minibatch of data.
  - y: A numpy array of shape (N,) containing training labels; y[i] = c means
    that X[i] has label c, where 0 <= c < C.
  - reg: (float) regularization strength

  Returns a tuple of:
  - loss as single float
  - gradient with respect to weights W; an array of same shape as W
  """
  # Initialize the loss and gradient to zero.
  loss = 0.0
  dW = np.zeros_like(W)
  num_train=X.shape[0]
  num_class=W.shape[1]
  index_tuple=(range(num_class),list(y))
  for i in range(num_train):
      scores=X[i].dot(W)
      scores_shifted=scores-scores.max()   #shift the value inside the vector
                                           #to make sure the highest value is zero 
                                           #in order to prevent the data blowup
      loss_i=-scores_shifted[y[i]]+np.log(sum(np.exp(scores_shifted)))
      loss+=loss_i
      for j in range(num_class):
          softmax_score=np.exp(scores_shifted[j])/sum(np.exp(scores_shifted))
          if j==y[i]:
              dW[:,j]+=(-1+softmax_score)*X[i]
          else:
              dW[:,j]+=softmax_score*X[i]
  loss/=num_train
  loss+=reg*np.sum(W*W)
  dW=dW/num_train+2*reg*W
  return loss, dW
```
- 从代码中可以看到上面这种代码在最优化的过程中整个训练样本的数据都参与到计算中的，每一次在计算导数的时候，整个数据集中的每一个数据都会参与到计算中。
### 梯度下降——Gradient Descent
　　当我们可以计算损失函数的梯度的时候，程序会重复地计算梯度然后对参数进行更新，这个过程称为梯度下降，核心思想就是我们一直跟着梯度走，直到结果不再变化。
- Mini-Batch gradient descent<br>
　　斯坦福的cs231n的notes中如下写道![](/downloads/mini_batch.png/)
　　什么意思呢，就是在现实问题中，我们会遇到训练样本非常庞大的问题，可能会达到上百万的级别。如果我们按照上面代码中使用的方法进行optimization的话，每次计算整个数据集上的损失函数值，然后进行一次参数的优化，这样做是非常浪费的，效率很低。
- 对比120万张图片的数据损失的均值和只计算1000张子集的数据损失的均值的时候，结果应该是一样的。实际情况中，数据集肯定不会包含重复的图像，那么小批量数据的梯度就是对整体数据集梯度的一个近似。因此，在实践中，通过计算小批量数据的梯度，可以实现更加快速的收敛，并以此来进行更频繁的参数更新。
- 在小批量数据策略中，有一个极端的情况，就是每一个批量中只有一个样本，这种策略叫做随机梯度下降(Stochastic Gradient Descent--SGD)。这种策略在实际中很少用到。虽然实际上是指代用一个数据来计算梯度，我们有时候还是会用SGD来代指小批量数据梯度下降。
### 知乎中关于SGD的一些解答
- 随机梯度下降：在每次更新的时候使用一个样本，随机也就是说我用样本中的一个例子来近似我们所有的样本，对于最优化问题，凸问题，虽然不是每一次迭代得到的损失函数都向着全局最优化的方向，但是大的整体的方向是向着全局最优解的．
- 批量梯度下降：在每次更新的时候使用全部的样本，在更新的时候，所有的样本都有贡献，计算出来一个标准梯度。
- mini-batch梯度下降：在每次更新的时候，我们使用b个样本，使用一些较小的样本来近似代替全部的。在深度学习中，这种方法是使用最多的。