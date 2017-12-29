---
title: Tensorflow笔记-构造神经网络
date: 2017-12-28 17:43:08
categories: Python
tags:
- Tensorflow
- 机器学习
- 神经网络
- 人工智能
---

![445H](PythonNeuralNetwork\445H.jpg)

# 前言

本文介绍了怎样建造一个完整的神经网络,包括构造添加神经层,构建所需数据，搭建神经网络，计算误差,训练步骤,让机器开始学习.

TensorFlow™ 是一个采用数据流图（data flow graphs），用于数值计算的开源软件库。节点（Nodes）在图中表示数学操作，图中的线（edges）则表示在节点间相互联系的多维数据数组，即张量（tensor）。用于机器学习和深度神经网络方面的研究，但这个系统的通用性使其也可广泛用于其他计算领域。

<!--MORE-->



```	python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# 构建神经网络

import tensorflow as tf
import numpy as np


# 1 构造一个神经层的函数
# 四个参数：输入值、输入的大小、输出的大小和激励函数，我们设定默认的激励函数是None
def add_layer(inputs, in_size, out_size, activation_function=None):
    # 生成初始参数时，随机变量会比全部为0要好很多，所以weights为一个in_size行, out_size列的随机变量矩阵
    Weights = tf.Variable(tf.random_normal([in_size, out_size]))
    # 机器学习中，biases的推荐值不为0
    biases = tf.Variable(tf.zeros([1, out_size]) + 0.1)
    # 定义Wx_plus_b, 即神经网络未激活的值。其中，tf.matmul()是矩阵的乘法
    Wx_plus_b = tf.matmul(inputs, Weights) + biases
    # 当activation_function——激励函数为None时，输出就是当前的预测值——Wx_plus_b
    # 不为None时，就把Wx_plus_b传到activation_function()函数中得到输出
    if activation_function is None:
        outputs = Wx_plus_b
    else:
        outputs = activation_function(Wx_plus_b)
    return outputs


# 2 导入数据
# 构建所需的数据。
x_data = np.linspace(-1, 1, 300, dtype=np.float32)[:, np.newaxis]
noise = np.random.normal(0, 0.05, x_data.shape).astype(np.float32)
y_data = np.square(x_data) - 0.5 + noise
# 利用占位符定义我们所需的神经网络的输入
xs = tf.placeholder(tf.float32, [None, 1])
ys = tf.placeholder(tf.float32, [None, 1])

# 2 搭建神经网络
# 构建的是——输入层1个、隐藏层10个、输出层1个的神经网络
l1 = add_layer(xs, 1, 10, activation_function=tf.nn.relu)  # 定义隐藏层
prediction = add_layer(l1, 10, 1, activation_function=None)  # 定义输出层
# 计算预测值prediction和真实值的误差，对二者差的平方求和再取平均。
loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys - prediction),
                                    reduction_indices=[1]))
train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)  # 最小化误差loss
init = tf.global_variables_initializer()  # 初始化变量
# 定义Session，并用 Session 来执行 init 初始化步骤
sess = tf.Session()
sess.run(init)

# 3 训练
for i in range(10000):
    # training
    sess.run(train_step, feed_dict={xs: x_data, ys: y_data})
    if i % 50 == 0:
        # to see the step improvement
        print(sess.run(loss, feed_dict={xs: x_data, ys: y_data}))

```

机器学习训练结果。误差越来越小，说明机器学习有积极的效果。

![result](PythonNeuralNetwork\result.png)

详细Tensorflow学习参考[这里](https://morvanzhou.github.io/tutorials/machine-learning/tensorflow/)！