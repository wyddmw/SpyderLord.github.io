---
layout: post
category: dump
title: 使用slim库进行模型的优化
description: 调用tensorflow的slim库进行模型的训练
---

```python
import tensorflow as tf
import tensorflow.contrib.slim as slim
       
vgg=tf.contrib.slim.nets.vgg
       
train_log_dir=''
if not tf.gfile.Exists(train_log_dir):
    tf.gfile.MakeDirs(train_log_dir)
    
with tf.Graph().as_default():
    images,labels=''
    
predictions=vgg.vgg16(images,is_training=True)
slim.losses.softmax_cross_entropy(predictions,labels)

total_loss=slim.losses.get_total_loss()
tf.summary.scalar('losses/total_loss',total_loss)

optimizer=tf.train.GraientDescentOptimizer(learning_rate=.001)
train_tensor=slim.learning.create_train_op(total_loss,optimizer)
#创建一个训练学习的op，调用tf.learning.create_train_op()函数就可以了，函数的参数有两个，一个是损失函数，另一个是优化器
slim.learning.train(train_tensor,train_log_dir)

```

