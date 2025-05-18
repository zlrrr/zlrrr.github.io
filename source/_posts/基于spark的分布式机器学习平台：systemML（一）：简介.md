---
title: 基于spark的分布式机器学习平台--systemML（一）：简介
date: 2019-02-13 05:26:21
tags:
---
### systemml系列介绍：导航

SystemML是IBM于2015开源的项目，是一个高度支持集群的机器学习/深度学习平台，现已孵化成为apache顶级项目。

![ml](基于spark的分布式机器学习平台：systemML（一）：简介/ml.gif)

<!--more-->
由于一些原因，SystemML在机器学习和深度学习领域的使用度还远不如目前热火朝天的TensorFlow，在国内的相关文章多为介绍，而相关的技术博客更是完全没有。
本系列将从systeml的基本介绍开始，从技术原理，环境配置，以及程序设计等方面对systeml做比较详细的剖析。

由于本人水平有限，难免出现理解上的偏差和描述上的不当之处，欢迎斧正。

##### 以下为整个系列的目录列表：
###### [基于spark的分布式机器学习平台：systemML（一）：简介](https://zlrrr.github.io/2019/02/13/%E5%9F%BA%E4%BA%8Espark%E7%9A%84%E5%88%86%E5%B8%83%E5%BC%8F%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E5%B9%B3%E5%8F%B0%EF%BC%9AsystemML%EF%BC%88%E4%B8%80%EF%BC%89%EF%BC%9A%E7%AE%80%E4%BB%8B/)
###### [基于spark的分布式机器学习平台：systemML（二）：部署指南](https://zlrrr.github.io/2019/03/15/%E5%9F%BA%E4%BA%8Espark%E7%9A%84%E5%88%86%E5%B8%83%E5%BC%8F%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E5%B9%B3%E5%8F%B0%EF%BC%9AsystemML%EF%BC%88%E4%BA%8C%EF%BC%89%EF%BC%9A%E9%83%A8%E7%BD%B2%E6%8C%87%E5%8D%97/)
###### [基于spark的分布式机器学习平台-systemML（三）：原理剖析](https://zlrrr.github.io/2019/03/20/%E5%9F%BA%E4%BA%8Espark%E7%9A%84%E5%88%86%E5%B8%83%E5%BC%8F%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E5%B9%B3%E5%8F%B0-systemML%EF%BC%88%E4%B8%89%EF%BC%89%EF%BC%9A%E5%8E%9F%E7%90%86%E5%89%96%E6%9E%90/)
###### [基于spark的分布式机器学习平台-systemML（四）：语法和库](https://zlrrr.github.io/2019/03/20/%E5%9F%BA%E4%BA%8Espark%E7%9A%84%E5%88%86%E5%B8%83%E5%BC%8F%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E5%B9%B3%E5%8F%B0-systemML%EF%BC%88%E5%9B%9B%EF%BC%89%EF%BC%9A%E8%AF%AD%E6%B3%95%E5%92%8C%E5%BA%93/)
###### [基于spark的分布式机器学习平台-systemML（五）：doc-trans-plan](https://zlrrr.github.io/2019/03/20/%E5%9F%BA%E4%BA%8Espark%E7%9A%84%E5%88%86%E5%B8%83%E5%BC%8F%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E5%B9%B3%E5%8F%B0-systemML%EF%BC%88%E4%BA%94%EF%BC%89%EF%BC%9Atrans-doc-plan/)

***

### systemml是什么？

在本简介中，我将基于官方文档来进行systemml的概述，其中的有关术语可能乍见之下难以有直观清楚的认知，我会在之后的内容中补充我自己的相关理解。

以下是官网给的简介，比较清楚地概述了systemml平台的目标定位：

Apache SystemML provides an optimal workplace for machine learning using big data. 
It can be run on top of Apache Spark, where it automatically scales your data, line by line, determining whether your code should be run on the driver or an Apache Spark cluster. 
Future SystemML developments include additional deep learning with GPU capabilities such as importing and running neural network architectures and pre-trained models for training.

翻译为中文如下：

systemml为使用大数据进行机器学习提供了一个优化的工作空间。
systemml运行于Apache spark之上，换句话说，就是将spark封装在内部进行自动的优化。
而spark作为一个流行的大数据处理框架，能够实现自动评估数据规模，决定代码是运行在本机硬盘上（小规模）还是运行在spark集群上（巨规模）。
同时，systemml也能够导入和运行神经网络模型进行训练，具有深度学习和将代码运行在GPU上的能力。

***

### Systemml的特点

SystemML is a flexible, scalable machine learning system. SystemML’s distinguishing characteristics are:

1.Algorithm customizability via R-like and Python-like languages.
2.Multiple execution modes, including Spark MLContext, Spark Batch, Hadoop Batch, Standalone, and JMLC.
3.Automatic optimization based on data and cluster characteristics to ensure both efficiency and scalability.

SystemML是一个灵活可扩展的机器学习系统，它有如下显著特点：
1.通过类似R和python的语言（DML语言）来客制化算法。
2.包括Spark MLContext，Spark Ba​​tch，Hadoop Batch，Standalone和JMLC在内的多种运行模式。
3.基于数据和集群特点的自动优化，能同时保证效率和可扩展性。

***

### Systemml与Tensorflow
**TensorFlow的流行：**
tensorflow的流行得益于近几年火热的深度学习。随着计算能力的普遍上升和并行计算，云计算的发展，深度神经网络的复杂量级参数训练和迭代过程对于现代计算机系统来说，已经不是一个难以解决的问题。


**Systemml的优势：**
systemml结合了多种机器学习算法和机器学习算法，具有TensorFlow不具有的全面性，此外systemml的分布式集群计算能力是建立在Apache的spark生态系统上的，而spark是大数据领域最优秀的处理框架之一。


**Systemml的劣势：**
TensorFlow拥有深度学习交流领域的几乎最大的开源社区。
而systemml在国内几乎无中文社区，这也在一个方面限制了systemml的发展速度，因为一个开源项目决定其发展速度的很大一方面即是其使用者和开发者的数量级。