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
　　

## 分割线2018-11-27-补充tensorflow中的对图像resize的程序
　　今天花了挺长的时间重新写了resize部分的代码，发现了一些非常有趣的事情：
- 使用opencv自带的函数进行resize的时候，单单是resize这部分的代码，运行的速度是非常快的，但是就像上面写到的那样，如果使用之前的测试程序进行测试的话，识别的准确率是非常低的，之前就怀疑过是不是因为缩放部分出了问题所以导致效果变差，今天测试了之后发现确实和这个缩放有一定的关系。
- opencv缩放之后的结果
![/downloads/opencv_result.jpg](/downloads/opencv_result.jpg)
- 原始图像直接输入之后的结果
![/downloads/original_result.jpg](/downloads/original_result.jpg)
- 自己写完tensorflow的resize函数之后的结果
![/downloads/tf_resize_result.png](/downloads/tf_resize_result.png)
　　很神奇的是使用自己写的函数经过缩放之后准确率竟然还提高了～<br>
　　把自己重新整理的使用tensorflow的程序放上来：

```python
# 其实加载这么多模块真正用上的好像就from tensorflow.python.ops import array_ops这一个语句
import os
import numpy as np
from tensorflow.python.framework import constant_op
from tensorflow.python.framework import dtypes
from tensorflow.python.framework import ops
from tensorflow.python.framework import tensor_shape
from tensorflow.python.framework import tensor_util
from tensorflow.python.ops import array_ops
from tensorflow.python.ops import check_ops
from tensorflow.python.ops import clip_ops
from tensorflow.python.ops import control_flow_ops
from tensorflow.python.ops import gen_image_ops
from tensorflow.python.ops import gen_nn_ops
from tensorflow.python.ops import string_ops
from tensorflow.python.ops import math_ops
from tensorflow.python.ops import random_ops
from tensorflow.python.ops import variables
import tensorflow as tf
import matplotlib.image as mpimg
import matplotlib.pyplot as plt
import cv2

def _ImageDimensions(image):
    """Returns the dimensions of an image tensor.
    Args:
      image: A 3-D Tensor of shape `[height, width, channels]`.
    Returns:
      A list of `[height, width, channels]` corresponding to the dimensions of the
        input image.  Dimensions that are statically known are python integers,
        otherwise they are integer scalar tensors.
    """
    if image.get_shape().is_fully_defined():
        return image.get_shape().as_list()
    else:
        static_shape = image.get_shape().with_rank(3).as_list()
        dynamic_shape = array_ops.unstack(array_ops.shape(image), 3)
        return [s if s is not None else d
                for s, d in zip(static_shape, dynamic_shape)]

def resize_image(image, size,
                 method=tf.image.ResizeMethod.BILINEAR,
                 align_corners=False):
    # Resize image.
    with tf.name_scope('resize_image'):
        height, width, channels = _ImageDimensions(image)
        image = tf.expand_dims(image, 0)
        image = tf.image.resize_images(image, size,
                                       method, align_corners)
        image = tf.reshape(image, tf.stack([size[0], size[1], channels]))
        return image

def preprocess(image,data_format):
    image=tf.to_float(image)
    #image=tf_image_whitened()
    image=resize_image(image,(300,300))
    if data_format=='NCHW':
        image = tf.transpose(image, perm=(2, 0, 1))
    return image                # 返回的是一个tensor

data_format='NHWC'
image_input=tf.placeholder(tf.uint8,shape=(None,None,3))
image_pre=preprocess(image_input,data_format=data_format)
#img_4d=tf.expand_dims(image_pre,0)

img=mpimg.imread('demo/changshu.jpeg')
print(img.shape)

with tf.Session() as sess:
    img=sess.run(image_pre,feed_dict={image_input:img})
    #cv2.imshow()
    img=np.array(img)
    img=img.astype(np.uint8)
    plt.imshow(img)
    plt.imsave('demo/changshu_resized.jpeg',img)
    plt.show()
    #print(img.dtype)

```

## 分割线2019-01-16 
　　将之前在笔记本上运行的模型尝试在tx2上运行，又遇到了tensorflow版本的问题，第一个问题是无法直接将在笔记本上生成的.pb文件加载进来进行读取，显示的错误在网上查找了之后发现是tensorflow版本的问题，在笔记本上使用的tensorflow是1.11，但是在tx2上使用的是tensorflow1.5版本。想到的第一种解决方案是去网上查找是否有合适的tensorflow1.11版本，但是这种经过专门编译过的tensorflow的版本.whl没有这么高的版本。所以只能尝试在tx2上解决出现的问题。开始的时候，直接在tx2上运行export_inference_graph.py然后将.ckpt文件转换为.pb文件，发现成功了，通过这种方式生成的.pb文件是直接能够运行的，但是第二天想将新训练出来的.ckpt文件转换为.pb文件的时候，程序再一次崩掉，报错的原因是之前没有见到的。

```python
TypeError: x and y must have the same dtype,get tf.float32!=tf.int32
```

　　这个错误是出现在训练完成后用命令生成可用模型的时候，网上给出了解决方案，需要代码进行修改：

```python
# /object_detection/builders/post_processing_builder.py
def _score_converter_fn_with_logit_scale(tf_score_converter_fn, logit_scale):
  """Create a function to scale logits then apply a Tensorflow function."""
  def score_converter_fn(logits):
    scaled_logits = tf.divide(logits, logit_scale, name='scale_logits')
    return tf_score_converter_fn(scaled_logits, name='convert_scores')
  score_converter_fn.__name__ = '%s_with_logit_scale' % (
      tf_score_converter_fn.__name__)
  return score_converter_fn

# 这是原始的程序 需要修改为
def _score_converter_fn_with_logit_scale(tf_score_converter_fn, logit_scale):
  """Create a function to scale logits then apply a Tensorflow function."""
  def score_converter_fn(logits):
    cr=logit_scale
    cr=tf.constant([[cr]],tf.float32)
    scaled_logits = tf.divide(logits, cr, name='scale_logits')
    return tf_score_converter_fn(scaled_logits, name='convert_scores')
  score_converter_fn.__name__ = '%s_with_logit_scale' % (
      tf_score_converter_fn.__name__)
  return score_converter_fn
```

　　修改完这部分之后再次运行，程序出现新的错误：

```python
ValueError: Protocol message RewriterConfig has no "optimizer_tensor_layout" field
```

　　google错误，出现相似的一个问题：

```python
ValueError: Protocol message RewriterConfig has no "layout_optimizer" field
```

　　问题猜测是官方更新代码的时候更新了接口的名字，导致文件之间不匹配了，强烈谴责！<br>
　　关于上面那个错误的问题，tx2上的解决方案是：

```python 
# /research/object_detection/exporter.py 第72行
# 将optimizer_tensor_layout更换为layout_optimizer
```
　程序运行成功，可以将.ckpt文件转换成为.pb文件然后读取。