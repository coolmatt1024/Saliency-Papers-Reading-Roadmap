ICLR 2015的一篇文章，个人觉得想法很大胆

一般来讲，改善网络性能的方法有两个方向：
扩展：更复杂的激活函数，改善的正则化，预训练
不同的CNN架构：VGG（池化层之间多个卷积），google inception（多个不同组件）

那么：CNN的哪个模块是必要的呢？？？

# 1 出发点

## 1.1 新问题（对传统的抨击，而且直击本质） 

经典网络的结构是conv-pool-fc
fc的存在可能还是因为CNN出现之前传统方法的影响，卷积池化只相当于特征提取，最后还要用分类器进行分类（后面在Network in Network中被抛弃）

**pool的存在是有道理的**，毕竟可以减少运算量并且具有一定的不变性

**可是pool的存在是必须的吗？？？**

## 1.2 老方法（分析功能，用卷积层来代替）

作者提出了全卷积网络：真的是全是卷积层，尺寸的减小是通过stride来实现的

## 1.3 闪光点

想法很大胆，分析很透彻，验证很巧妙，得出的结论也很beautiful

# 2 具体实现

## 2.1 理论分析

卷积是加权和，池化是p范数的一种特殊形式

> The pooling layer can be seen as performing a feature-wise convolution in which the activation function is replaced by the p-norm.

**pooling在CNN的作用：**

 - the p-norm makes the represen- tation in a CNN more invariant
 - the spatial dimensionality reduction performed by pooling makes covering larger parts of the input in higher layers possible
 - the feature-wise nature of the pooling operation could make optimization easier

 - p范数使CNN的特征更具有不变性（提取更显著的特征）
 - 池化使高层的接受野更大
 - 优化更简单

PS：我觉得第一点更重要、第二点也很有道理（作者其实只针对了第二个just try）

## 2.2 改进

 1. 去掉pooling，前面一层stride相应增加
 2. 将池化层用一个卷积层(k=3,s=2)来代替

*另外，作者用来验证的模型都是很简单的模型，卷积核也都很小（甚至有1x1），这也是发展趋势并且得到验证的（相关论文还没看）*   

*作者网络的广度也就十层，可是得到的结果却很好（到时候设计可以参照作者的结果）*

## 2.3 作者训练网络的方法
![此处输入图片的描述][1]

![此处输入图片的描述][2]

## 2.4 网络可视化（没仔细看，可以借鉴）

*为了说明自己网络的性能很棒，作者还提出了一种guided bp的方法*

可以看出，作者的效果很棒！！！

![此处输入图片的描述][3]

# 3 实验结果

ALL-CNN的性能竟然可以达到90%，简直是棒

# 4 总结

## 4.1 作者的看法
作者概括了自己的贡献：

 - very simple architec- tures may perform very well
 - pooling operations does not always improve performance of CNNs
 - propose a new method of visualizing the representations（it produces sharper visualizations of descriptive image regions than the previously known methods, and can be used even in the absence of ’switches’）
 - 
作者还指出
this paper is not meant to discourage the use of pooling or more sophisti- cated activation functions altogether.

## 4.2 我的看法

这篇文章真的是很简洁有力啊，有空多看看

 
  [1]: http://a1.qpic.cn/psb?/V14DGBA82xqcTT/HWlqA94NET7.UkH84MGDKVeQs6Py94LAuUEm5tqgP4c!/b/dLEAAAAAAAAA&bo=6QPeAQAAAAARAAI!&rf=viewer_4
  [2]: http://a1.qpic.cn/psb?/V14DGBA82xqcTT/hhmDq7hpiqAmQQXXh1GGVt0b5d04v.OKq4ShPIYlgQY!/b/dBoBAAAAAAAA&bo=4gOXAAAAAAARAEE!&rf=viewer_4
  [3]: http://a2.qpic.cn/psb?/V14DGBA82xqcTT/Zil6.j63M6Yxm1DRFDAmELxrgsFUv2wamaEyVnZfa0Y!/b/dB4BAAAAAAAA&bo=TAM1AQAAAAARAEw!&rf=viewer_4
