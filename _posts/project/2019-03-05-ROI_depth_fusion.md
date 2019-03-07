---

layout: post
category: project
title: ROI_depth_estimation
description: 目标检测和深度估计的联合训练网络，反正就是拼拼凑凑就好了，写是不会写的～
---

## Multi-task training for object detection and depth estimation aimed at ROI depth estimation based on a monocular image
　　先来一个大气又冗长的标题，基本上表达了想要设计网络模型的目的是什么——设计一个多任务联合训练的模型，模型的输出是两个分支，分别对应的是目标检测任务和深度估计任务。但是这并不是网络的全部内容，只是在训练阶段的模型结构，是一个多任务encoder-decoder的结构，在预测的时候，并不会在全局上进行深度的估计，建立在目标检测得到的ROI的基础上，只在特定的区域内进行深度的估计，减少模型的运算量，下面会给出模型在训练和预测时候各自对应的网络结构。<br>

## Multi-task training network in tensorflow
　　多任务联合训练通过tensorflow的编程是可以实现的：

```python
import tensorflow as tf
import numpy as np

# numpy制造两组假数据
x_data=np.float32(np.random.rand(2,100))
y1_data=np.dot([0.100,0.200],x_data)+0.300
y2_data=np.dot([0.500,0.900],x_data)+3.00

b1=tf.Variable(tf.zeros([1]))
w1=tf.Variable(tf.random_uniform([1,2],-1.0,1.0))
y1=tf.matmul(w1,x_data)+b1

b2=tf.Variable(tf.zeros([1]))
w2=tf.Variable(tf.random_unifor([1,2],-1.0,1.0))
y2=tf.matmul(w2,x_data)+b2

#损失函数部分
loss_1=tf.reduce_mean((y1-y1_data)**2)
loss_2=tf.reduce_mean((y2-y2_data)**2)

#构造优化器
optimizer=tf.train.GradientDescentOptimizer(0.5)
train1=optimizer.minimize(loss_1)
train2=optimizer.minimize(loss_2)

#初始化全局变量
init=tf.global_variables_initializer()
with tf.Session() as sess:
    sess.run(init)
    for i in range(100):
        #如果我们只是想执行其中的一组数据，尝试一下：
        sess.run(train1)
        print(sess.run(w1),sess.run(b1))
                   
#多任务联合训练的方法有两种，一种是交替训练，另一种是何在一起训练，根据我们的数据集是什么样的，选择不同的训练方式

```

```python
import tensorflow as tf
import numpy as np

#定义占位符
x=tf.placeholder('float',[10,10],name='X')
y1=tf.placeholder('float',[10,20],name='Y1')
y2=tf.placeholder('float',[10,20],name='Y2')

#定义权重
initial_shared_layer_weight=np.random.rand(10,20)	# the shape is 10*20
initial_Y1_layer_weight=np.random.rand(20,20)
initial_Y2_layer_weight=np.random.rand(20,20)

shared_layer_weights=tf.Variable(initial_shared_layer_weight,name='share_W',dtype='float32')
Y1_layer_weights=tf.Variable(initial_Y1_layer_weight,name='Layer1',dtype='float32')
Y2_layer_weights=tf.Variable(initial_Y2_layer_weight,name='Layer2',dtype='float32')

#使用ReLU来构建layer
shared_layer=tf.nn.relu(tf.matmul(x,shared_layer_weights))
y1_layer=tf.nn.relu(tf.matmul(shared_layer,Y1_layer_weights))
y2_layer=tf.nn.relu(tf.matmul(shared_layer,Y2_layer_weights))

#计算损失函数
y1_loss=tf.nn.l2_loss(y1-y1_layer)
y2_loss=tf.nn.l2_loss(y2-y2_layer)

#优化器
#Y1_op=tf.train.AdamOptimizer().minimize(y1_loss)
#Y2_op=tf.train.AdamOptimizer().minimize(y2_loss)
Joint_loss=y1_loss+y2_loss
optimiser=tf.train.AdamOptimizer().minimize(Joint_loss)
Y1_op=tf.train.AdamOptimizer().minimize(y1_loss)
Y2_op=tf.train.AdamOptimizer().minimize(y2_loss)


with tf.Session() as sess:
    init=tf.initialize_all_variables()
    sess.run(init)
    #专门做了这样的测试，也即是在feed_dict的时候可以只输入其中一条分支所需要的数据
    for i in range(10000):
        _,Y1_loss=sess.run([Y1_op,y1_loss],feed_dict={
            x:np.random.rand(10,10),
            y1:np.random.rand(10,20)
        })
        print(Y1_loss)


```

## I have something to say 

　　昨晚上在朋友圈看到有同学被康奈尔大学研究生录取了，当然了首先应该为这位同学被世界顶级学府录取感到高兴，高兴之余又想了点东西。我和他的方向还有些像，都是和深度学习相关的，具体他是做视觉还是自然语言处理我记不清了，我保送的时候有见过这位同学，我当时还没有任何着落，在国内的一些院校中疯狂地发着简历和邮件。问他想去哪，当时有提到帝国理工，好像也有康奈尔。当时他也是在做着准备工作，等待这offer。对于优秀的标准，我总是不经意习惯将吴丹作为一个评价的标准，因为都是计算机专业，她作为一个女生有着自己成熟的心智还有坚强，她也要出国了，但是最近没有什么联系，不知道现在进展如何了。当时她告诉我的是，感觉top级别的大学申请不下来了，因为没有科研经历不够，也没有什么论文。今天早上和黄子纯的morning call，我们聊到了这个问题。对于科研这个概念，我是很模糊的。我觉得说道科研，首先要有自己的创新点和想法在里面。如果只是照着课本重复性的去做一些实验的复现，或是将现成的东西应用到某一个具体的问题，我觉得那叫做工程或者动手更合适一些。站在距离毕业还有四个月左右的节点上，我自己的大学生活中又做了哪些东西呢？好像一直在动手，但是依然手残，C++不会写，tensorflow读不懂，曾经引以为傲的一个项目——大学生科创计划，后来和一起完成的朋友说起来时，也一致觉得，根本没有什么好值得骄傲的。但好在自己能吹的天花乱坠，蒙骗了一些老师，让我最后叩开了同济的大门。在没有开始实习之前，可以说自己是没有什么创造力的吧。在自动化所里，一遍做着目标检测的学习，一边完成着同济老师布置的深度估计的学习——我常给黄子纯说自己是同济和中科院联合培养的学生，她戏谑说因为太弱了所以需要两个高校来一起培养(手动捂脸.jpg)。当时身边的同事做的是毫米波雷达的融合，和距离的检测也有关系，就问他障碍物检测这个任务对于深度距离的信息是不是也很需要，其实自己想想也是非常重要的。只是在二维的平面做目标检测其实实用性是很低的，没有深度信息，映射不到世界坐标系中，对于车辆上层的决策其实还是缺少有效的信息。当时就想了，如果我能做出来深度估计和目标检测端到端的输出，那岂不是省去了后期和激光雷达融合的步骤了吗？当时的出发点是为了减少运算量，提高模型的实时性，更简单地说，更像是去做拼图，将两个模块拼起来。虽然之前看过一个多任务联合训练的论文MultiNet，但是对Multi-task这样的任务并没有什么概念，只是知道可以一个输入，多个输出，稀里糊涂就想做一个这样的模型。最开始的时候给贾臻伟说了自己的想法，觉得是一个不错的想法。给同济的老师说了之后更是得到了大力支持，同意我毕设的时候自己做这个题目。随着任务的不断推进，看了如何联合训练的方法，了解到多任务的模型在训练的时候是不同任务之间是可以相互影响到的，意识到如果加入到深度的信息说不定可以改进目标检测的效果。开心啊，说不定自己的这个想法就改变世界了？而且我最开始设计的模型是在检测出目标物体的区域内做深度的估计，原因也很简单，从人的行为出发，我们也不需要得到视线所及范围全部区域的深度信息，我觉得只是在影响到我们做决策的区域的深度信息才是有用的，所以在全局上做深度的估计是一种计算的浪费。

　　早上起来看一个公众号，讲的是3D-BBX在目标检测中的方法，在2018年的CVPR等会议中的论文中，有三篇论文都是将深度的信息加以融入。其中微软亚洲研究院的论文更是直接提出了instance depth的概念，做的事情就是我想的在ROI区域里面进行深度估计的事情。不禁为自己从产生这个想法到一步步写程序去验证想法到现在开始做自己的模型融合感到开心，在没有任何资料查询的基础上，想出来的这个idea没想到也是很有 价值的一个点，而且微软还把它做出来了，发了CVPR。

　　写了这么多，要接上最开始的内容——不要让自己所处的环境限制了自己的发展，在常春藤也好，在国内重本也好，人才是决定问题的关键因素，所以不要让自己的进步被环境拖了后腿，外界环境不背锅。