---
layout: post
category: dump
title: tensorflow的session
description: graph session tensor opimport_graph_def
---

　　TensorFlow是基于图的计算系统。而图的计算节点是由operation来构成的，而图的各个节点之间则是由张量(Tensor)作为边来连接在一起的。所以TensorFlow的计算过程就是一个Tensor流图。Tensorflow的图则是必须在一个Session中来计算。Session提供了Operation执行和Tensor求值的环境。

```python
import tensorflow as tf
a=tf.constant([1.0,2.0])
b=tf.constant([3.0,4.0])
c=a*b
sess=tf.Session()
print(sess.run(c))
sess.close()
```

　　一个Session可能会拥有一些资源，比如说变量等或者queue。当我们不需要该session的时候，需要将资源释放。有两种方式：
- 调用session.close()
- 使用with tf.Session()创建上下文(context)来执行，当上下文退出时，自动释放。<br>
　　如果在创建Session的时候没有指定Graph，那么session会加载默认的Graph。<br>

```python
#看一下session的构造函数：
tf.Session._init_(target='',graph=None,config=None)
```
　　执行Operation或者求值Tensor，有两种计算的方法：
- 调用Session.run()方法:该方法的定义如下，参数fetches便是一个或多个Operation或者Tensor。

```python
#方法一
tf.Session.run(fetches,feed_dict=None)
```

- 调用Operation.run()或是Tensor.eval()方法，这两个方法都接收参数session，用于指定在那个session中进行计算。但是这个参数是可以选择的，默认是None，表示在默认的session中进行计算。设置一个session为默认的session有两种方法：
- 在with语句中定义的session，在该上下文中便成为了默认的session:

```python
import tensorflow as tf
a=tf.constant([1.0,2.0])
b=tf.constant([3.0,4.0])
with tf.Session():
  print(c.eval())
```

- 在with语句中调用Session.as_default()方法。上面的例子可以改成

```python
import tensorflow as tf
a=tf.constant([1.0,2.0])
b=tf.constant([3.0,4.0])
c=a*b
sess=tf.Session()
with sess.as_default():
    print(c.eval())
sess.close()
```

## Graph
　　tensorflow中使用tf.Graph类表示可以计算的图。图是由操作Operation和张量Tensor来构成，其中Operation表示图的节点(即计算单元)，而Tensor表示图的边(即Operation之间流动的数据单元)。在tensorflow中，始终存在一个默认的Graph。如果要将Operation添加到默认Graph中，只需要调用定义Operation的函数(例如tf.add())。如果我们需要定义多个Graph，则需要在with语句中调用Graph.as_default()方法将某个graph设置成默认的Graph，于是with语句块中调用的Operation或是Tensor将会添加到这个Graph里面。

```python
import tensorflow as tf
g1 = tf.Graph()
with g1.as_default():
  c1=tf.constant([1.0])
with tf.Graph().as_default() as g2:
  c2=tf.constant([2.0])
with tf.Session(g1) as sess1:
  print(sess1.run(c1))
with tf.Session(g2) as sess2L:
  print(sess2.run(c2))
```

　　如果将上面run中的c1和c2调换一下，程序会报错，因为c1是定义在g1图中的，c2是定义在g2图中的，sess1加载的g1中没有c2这个tensor，sess2加载的g2中没有c1这个tensor。

## Operation
　　一个Operation就是TensorFlow Graph中的一个计算节点。其接收零个或者多个Tensor对象作为输入，然后产生零个或者多个Tensor对象作为输出。当一个Graph加载到一个Session中，则可以调用Session.run(op)来执行op，或者调用op.run()来执行

## Tensor
　　Tensor表示的是operation的输出结果。不过，tensor只是一个符号句柄，其并没有保存operation输出结果的值。通过调用Session.run(tensor)或者tensor.eval()方法可以获取该Tensor的值。