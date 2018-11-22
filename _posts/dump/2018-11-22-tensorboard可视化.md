---
layout: post
category: dump
title: TensorBoard可视化查看模型的graph
description: 使用tensorboard对保存下来的模型的graph进行查看
---

```python
#要想查看模型的gragh文件，需要对应的graph文件，分别是.meta或者是.pb文件
import tensorflow as tf
import os

log_dir='/home/spyder/SSD-Tensorflow/ssd/logdir'
if not os.path.exists(log_dir):
    os.mkdir(log_dir)
with tf.Session() as sess:
    with tf.gfile.FastGFile('/home/spyder/SSD-Tensorflow/ssd/frozen_inference_graph.pb','rb') as f:
        graph_def=tf.GraphDef()
        graph_def.ParseFromString(f.read())
        tf.import_graph_def(graph_def,name='')
    writer=tf.summer.FileWriter(log_dir,sess.graph)
    writer.close()
```

　　如果我们手边存在的是.meta文件，也是可以对.meta文件进行处理然后tensorborad进行可视化的。

```python
import tensorflow as tf
log_dir=''
with tf.Session() as sess:
  _=tf.train.import_meta_graph('logdir.meta')
  graph=tf.get_default_graph()
  writer=tf.summary.FileWriter(log_dir,sess.graph)
  writer.close()
```

　　启动tensorboard进行可视化的时候，在终端输入对应的命令行：

```python
tensorboard --logdir #输入自己对应的log日志路径就可以了
```
　　在网页上看结果：localhost:6006
