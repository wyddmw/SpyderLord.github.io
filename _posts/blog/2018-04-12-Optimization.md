---
layout: post
category: blog
title: Optimization
description: parameter updates
---

## Optimization and Parameter Update
```python
def _fc_layer(self, bottom, name, num_classes=None,
            relu=True, debug=False):
      with tf.variable_scope(name) as scope:
      shape = bottom.get_shape().as_list()

      if name == 'fc6':
            filt = self.get_fc_weight_reshape(name, [7, 7, 512, 4096])
      elif name == 'score_fr':
            name = 'fc8'  # Name of score_fr layer in VGG Model
            filt = self.get_fc_weight_reshape(name, [1, 1, 4096, 1000],
                                                num_classes=num_classes)
      else:
            filt = self.get_fc_weight_reshape(name, [1, 1, 4096, 4096])
      conv = tf.nn.conv2d(bottom, filt, [1, 1, 1, 1], padding='SAME')
      conv_biases = self.get_bias(name, num_classes=num_classes)
      bias = tf.nn.bias_add(conv, conv_biases)

      if relu:
            bias = tf.nn.relu(bias)
      _activation_summary(bias)

      if debug:
```
   
