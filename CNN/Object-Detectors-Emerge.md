> Zhou, B., Khosla, A., Lapedriza, A., Oliva, A., & Torralba, A. (2014). Object Detectors Emerge in Deep Scene CNNs. Retrieved from http://arxiv.org/abs/1412.6856


# 0 准备工作

## 0.0 datasets

ImageNet： 1.3 million images，1000 object categories(ILSVRC2012); top 1 accuracy 57.4%
Places: 2.4 million images, 205 scene categoris; top 1 accuracy 50.0%

## 0.1 architecture

# 1 出发点

## 1.1 本质上还是一个神经网络可视化的问题

深度神经网络取得了显著的性能，几乎是碾压手工特征；然而手工特征清楚明白，神经网络却像一个黑盒子，我们不知道它到底学到了什么；而且这样的一个特征提取器，有很好的泛化性能，这就更让人吃惊它究竟学到了什么

## 1.2 现有的方案（unfinished）

### 1.2.1 反卷积,这个我一直不太懂

> We present a novelway to map these activities back to the input pixel space, showing what input pattern originally caused a given activation in the feature maps.

Zeiler, M. and Fergus, R. Visualizing and understanding convolutional networks. In ECCV, 2014.

### 1.2.2 最大化某个激活或者类得分

### 1.2.3 BP算法

### 1.2.4 其他（unfinished）

这篇文章还提到了两篇，感觉不太一样，可以看一下

## 1.3 作者的想法

首先，当所有人都在研究ImageNet-CNN的时候，作者竟然想到了Places-CNN（我们都认为ImageNet-CNN很优秀，可是却没想过它的数据集决定了这个网络的特征）

ImageNet-CNN是用来做目标分类的，潜在的表示应该是什么很难说明；实际上，目标经常用part-based representations来表示，可是part是什么没有定论(DPM????记得好像是有结论的啊)[存疑，我觉得object分类可能得到的就是object part]

Places-CNN就不一样了，它的表示可能更清晰，**目标可能是场景的一个distributed code for scenes**,当然还有textures,bag of words,part-based model,Object Bank（确实了解传统的也是很有必要的）

所以，这篇文章通过猜想和实验验证（类似于遮挡实验，不过不是观察最后的结果，而是观察某个神经元的激活）得到，目标确实会出现在Places-CNN中；在这一点的基础上，可能还有很多可以做的东西

# 2 Object Detectors Emerge in Deep Scene CNN

## 2.1 Uncovering the respresentation（说白了就是可视化）

### 2.1.1 simplifying the input images

作者说这是一个著名的检测人类识别的策略（很好的思路），用于提取图像中的重要信息

**第一种方法就是分割图像，不断去掉没有贡献的区域，剩下的就是最有价值的信息**

![此处输入图片的描述][1]

实际上这种方法并不能揭露目标的重要性，因为分割很大程度上就是基于目标的。但可以揭露目标的相对重要性，比如床就比墙要重要得多。

**第二种方法更彻底，直接使用SUN数据集，可以直接揭露那个目标对场景分类更重要。比如，对卧室而言，87%的情况下最小化的表示保留了床（28% walls和21% window）**

*作者通过简化图像，说明了目标对分类的重要性（而不是语境？？？），也因此推测，最后得到的representation很可能就是object*

## 2.2 可视化接受野和它们的激活样式

虽然每一个unit对应的接受野可以计算，但是实际是什么激活了它们才是我们感兴趣的问题（所以一开始的patch到后面的deconv）

作者通过遮挡实验（11x11，stride=5，共5000多个滑窗），来找出哪一个小区域（比patch小得多）对激活的输出影响很大，得到所谓的discrepancy map，后面又做了校正（不太明白怎么做的），从而得到每张图片的实际接受野。


**接受野的大小**
![此处输入图片的描述][2]

**基于接受野分割图像（重要）**
![此处输入图片的描述][3]

这样就得到了某个单元的激活样式（比patch更具体和准确）

## 2.3 明确内部单元的语义

有了可视化的结果，下一步就可以明确每一层的每个单元得到了什么样的表示

作者在Amazon Machanical Turk（AMT）上,对每个单元的top-60图片进行分类(如tower、bed等)，然后看一下top-60中与这个语义相关的准确性，最后对这个语义进行分类(重点)

### 2.3.1 语义是统一的吗？？？是！！！
![此处输入图片的描述][4]

 - 看出准确性非常高，说明每一个unit的语义是有很大一致性的
 - Places第一层的语义和最后一层的语义要略高于最后一层，可能第一层是简单纹理，最后一层是object，相比什么object part有较为明确的语义（具体那个description是怎么弄的不太清楚）

### 2.3.2 是哪一类语义？？？Object的语义大约有28%
![此处输入图片的描述][5]

对于Places-CNN，
不断由简单特征聚合成复杂特征（object、scene）

对于ImageNet-CNN，
不断由简单特征聚合成复杂特征（object part、object）

### 2.3.3 是哪一种语义？？？建筑、树、草……；狗、鸟、人、轮子

使用全标注的SUN database（8220 fully annotated images，205 places category）进行训练

![此处输入图片的描述][6]

有十五个unit检测建筑

### 2.3.4 为什么出现这些语义（而不是其他的）？？？数据集

![此处输入图片的描述][7]

 - 目标在数据集出现的频率，相关性0.54
 - 用于分辨场景的类，相关性0.84

个人感觉可能是数据集中object的多样性

### 2.3.5 剩下不是目标的语义（115units）干啥的？？？补充的！！！

可能是由于不完善的学习（真敢怀疑）和补充的基于纹理和基于part的表示

# 3 结论

作者通过可视化unit和统计unit的语义，得出了一个很nice的结论（之前Deconv那么看说是什么样的语义不具有准确性）：object emerges in deep scenes

在此基础上可以做什么，作者没有提，但是提了一个目标定位（个人感觉通过滑窗的方法效率太低，准确性也不好）
 
  [1]: http://img1.ph.126.net/xBiH4LJDj6v-xSToxzeeMQ==/2605613859428926003.png
  [2]: http://img1.ph.126.net/GUTtXyTm36TqKxrqdFWFuQ==/1275926069447788582.png
  [3]: http://img0.ph.126.net/F3CfYS8oUc6IFuv7y6NgHg==/6632586191259462714.png
  [4]: http://img0.ph.126.net/dFNV2Mdhn6fOytI-MOhYzg==/6632366288933896809.png
  [5]: http://img2.ph.126.net/w7uQiNX_WNXKpB9fow2xMw==/6632236546560303596.png
  [6]: http://img1.ph.126.net/36YNMyJRHZPNCagYQlGRWQ==/2592947485476963907.png
  [7]: http://img0.ph.126.net/Aw9cSBmPoisLm3VkYEiSxQ==/2596325185197487332.png
