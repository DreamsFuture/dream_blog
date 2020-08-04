


---
title: Notes for  paper 《radar signal modulation recognition based on deep joint learning》
date: 2020-08-01T11:18:32+08:00
lastmod: 2020-08-01T11:18:32+08:00
author: Dreamsfuture
authorlink: https://dreamsfutureblog.utools.club
# cover: /img/performance_indexes.jpg
background: img/background.jpg
categories: ["AI", "Communication"]
tags: ["Machine-Learning", "Deep-Learning", "Radio", "Communication", "Radar"]
# showcase: true
draft: false

---


## A Novel Attention Cooperative Framework for Automatic Modulation Recognition
## basic information

|   项目   | 内容                                                         |
| :------: | :----------------------------------------------------------- |
| 引用格式 | Chen S, Zhang Y, He Z, et al. A Novel Attention Cooperative Framework for Automatic Modulation Recognition[J]. IEEE Access, 2020: 15673-15686. |
| 期刊水平 | IEEE Access（Q1 / JCR 2）                                    |
|   分类   | AMC                                                          |
|   源码   |                                                              |
|   评价   | 1、GAN扩充数据集，并且实验显示效果不错<br>2、CCNN和BRNN提取时空特征，注意力机制融合，其实是有些老套的，但是效果还可以 |

## Abstract

本文提出了一种基于构建了卷积神经网络(CNN)、递归神经网络(RNN)和生成对抗网络(GAN)结合的方法，贡献点包括：

- 循环连接神经网络(CCNN)提取接收信号的空间特征，双向连接神经网络(BRNN)提取接收信号的时间特征，Global pooling和max pooling融合
- 注意力机制校准特征图重要程度
- ACGAN生成信号扩展数据集



## Model

![image-20200722012801846](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722012801846.png)

### 贡献点1 时空特征部分

时间特征：BRNN

空间特征：CCNN

时空融合：GAMP

![image-20200722013245282](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013245282.png)

![image-20200722013258712](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013258712.png)

![image-20200722013327488](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013327488.png)

![image-20200722013414053](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013414053.png)

![image-20200722013442740](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013442740.png)

![image-20200722013502709](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013502709.png)

### 贡献点2 数据扩充GAN

![image-20200722012951369](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722012951369.png)

1、**基于线性正则变换的交叉模糊函数**

![image-20200722010920871](../../source/images/Note-2019-Electromagnetic%20Signal%20Classification%20Based%20on%20Deep%20Sparse%20Capsule%20Networks/image-20200722010920871.png)

![image-20200722010940293](../../source/images/Note-2019-Electromagnetic%20Signal%20Classification%20Based%20on%20Deep%20Sparse%20Capsule%20Networks/image-20200722010940293.png)

2、**基于线性正则域的交叉模糊函数**

![image-20200722011009098](../../source/images/Note-2019-Electromagnetic%20Signal%20Classification%20Based%20on%20Deep%20Sparse%20Capsule%20Networks/image-20200722011009098.png)

![image-20200722011025090](../../source/images/Note-2019-Electromagnetic%20Signal%20Classification%20Based%20on%20Deep%20Sparse%20Capsule%20Networks/image-20200722011025090.png)

3、**等间隔的高阶振幅谱**

![image-20200722011049025](../../source/images/Note-2019-Electromagnetic%20Signal%20Classification%20Based%20on%20Deep%20Sparse%20Capsule%20Networks/image-20200722011049025.png)

![image-20200722011059778](../../source/images/Note-2019-Electromagnetic%20Signal%20Classification%20Based%20on%20Deep%20Sparse%20Capsule%20Networks/image-20200722011059778.png)



## Experiment

数据集：RML2016.10a，BPSK, QPSK, 8PSK, QAM16, QAM64, CPFSK, GFSK, 4PAM, WBFM, AM-DSB and AM-SSB,

参数设置：信噪比0、5、10，训练样本4w

对比方法：CNN-AMC，ML-AMC，9阶累积量 feature based method

- RNN

双向RNN更好，上下文时间信息能够充分提取

![image-20200722013643113](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013643113.png)

- 注意力机制

![image-20200722013741238](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013741238.png)

![image-20200722013922636](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013922636.png)

更好，自注意机制捕获了特征的内部相关性，提高了时间特征表征的精度。

- 循环结构

![image-20200722013838814](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722013838814.png)

 循环结构更好，这是因为循环结构每两层之间建立向和反向连接，提高了特征的可重用性，两阶段数据传播实现了循环反馈操作，实现了特征细化和空间注意机制。

- GAN

![image-20200722014023060](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722014023060.png)

生成的数据有效，但是当数据量超过50%的时候不再提升，因为生成的数据毕竟分布有差异

- 方法对比

![image-20200722014140339](../../source/images/Note-2020-A%20Novel%20Attention%20Cooperative%20Framework%20for%20Automatic%20Modulation%20Recognition/image-20200722014140339.png)

明显优于其他方法