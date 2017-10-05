# 1 作者的出发点

## 1.1 新问题

there is no clear understanding of why they perform so well, or how they might be im- proved.

为什么神经网络可以表现的如此之好？？？怎样去改善神经网络？？？

**这篇文章应该是12年后可视化CNN的开山之作**

## 1.2 老方法

Deconv最开始是 Zeiler做的工作，初衷是提取稀疏特征之类的，但并不成功（而且公式丑的不要不要的）

Deconvolutional Networks[1]

Adaptive Deconvolutional Networks for Mid and High Level Feature Learning[2]

> 通过deconvolutionalnetwork学习图片的特征表示，和上一篇不同的是加入了pooling，unpooling，deconv(transpose conv，deconv的参数只是原卷积的转置，并不原卷积的可逆运算)。这篇文章才是可视化常用的反卷积，上篇文章的deconv只是说conv的方向从feature map到图片，也还是feedforward的概念，不是这篇里用的conv和transpose conv。

**注意，第二篇的Deconv就是后面用的Deconv，但是第二篇是来提取特征的，当被用于可视化时，效果很好**


## 1.3 闪光点

可视化CNN的开山之作

可以对神经网络的特征进行分析，从而判断神经网络的性能，对神经网络的架构、训练都有很好的帮助

# 2 实现方法（技术手段）——反卷积

## 2.1 反卷积

### 2.1.1 关于反卷积的一点小说明：

> 大家也都知道了这里的 Deconvolution 不是信号处理中的 Deconvolution（把convolution处理过的信号，尽最大可能的还原回去），这里只是想说明 Convolution把图片变小，**为了完成某些任务需要把图片再变大回去**，所以叫 Deconvolution（因为 Convolution的叫法本身也是错误的）
> 
> 大多数人所说的更合适的叫法是**Transposed Convolution**，有两个原因：它就是 Convolution Network 的一种镜像，所以叫Transposed Convolution。从代码实现上，他就是把 filter 取了转置（Transpose），而这种做法又跟 backwards propagation 的代码实现一样，所以又叫 **Backwards Convolution**。这两种叫法都与代码实现上有很大很大的关系。

### 2.1.2 反卷积的实现

**反卷积是一种将图像从中间层映射到像素空间的方法（可以用于无监督学习、可视化、上采样和生成图像）**

**【注意反卷积不是重构图像，比如可视化就是将梯度反向传播的一种方法】**

卷积：low-layer feature->**conv->relu->pool**->high-layer feature
反卷积：high-layer feature->**unpooling->rectification->filtering**->low-layer feature

**和原文的说法略有不同（借鉴了[1]，感觉作者的思路还是重构图像的思路）**

*Unpooling：（肯定要结合switch）是将梯度放回原来的位置（和反向传播相似）
Rectification：原来是只通过正的特征（负的特征没有贡献）[不太懂，可能就是神经元的抑制和激活吧]；我们现在只通过正的梯度（也就是对激活有正影响的部分）[不明白意义何在]
filtering：乘以滤波器的转置，求梯度的链式法则（和反向传播相似）*

PS：整个过程是不需要学习的（滤波器那儿只是原来的滤波器转置，池化那儿只是简单记录一下feedforwad的位置就好了）

[1] Deep Inside Convolutional Networks: Visualising Image Classification Models and Saliency Maps

### 2.1.3 一点延伸

关于后向传播、deconv和后面的guided backpropagation本质上都是后向传播，只是对待relu的方式不同[具体为什么这样也不是很懂，但是从结果上来看还是蛮好的]

反卷积的用处很多，但是本质上都是从特征到图像空间的映射

#3 基于反卷积对网络的特征分析

## 3.1 特征分析

 1. 从验证集中选择top 9 activation（更易观察到显著的结果）
 2. 只保留某一个激活（这里的一个激活是指某一层滤波器的一个局部连接），即HxWxC中的某个C中某个(i,j)【还是对一些基本术语不了解】，其他全置0
 3. 利用反卷积进行可视化，就可以得到最大激活该神经元的输入

从得到的结果来看，比直接观察patch有更好的效果

先说基本结论：
**展现了特征在网络中的层次属性**
Layer2对应的是corners和边缘或者颜色的连接
Layer3对应的是相似的纹理、样式；文字
Layer4对应的是更多的基于类
layer5对应的是具有不同姿势的物体

## 3.2 特征演变

![此处输入图片的描述][1]

底层的特征很快收敛（几个epoch），高层特征需要很多轮次才能收敛（40-50 epochs）


## 3.3 特征不变性

![此处输入图片的描述][2]

横向比较：小的变换对底层特征影响很大（注意纵轴的数值），对高层特征的影响较小

单看最后一列：模型对于较大范围的平移和缩放有较好地适应性，对于旋转的适应性较差

# 4 通过可视化特征来改善架构

![此处输入图片的描述][3]

## 4.1 滤波器(第一层)
AlexNet的第一层滤波器大部分通过的是高频和低频信息，中频信息很少。

## 4.2 第二层特征（可视化）

AlexNet特征可视化有一些走样，作者认为是由于第一层较大的stride造成的，所以将第一层的stride由4改成了2，得到了更清晰的结果

# 5 一些有意思的实验结果

## 5.1 ImageNet的分类性能（过时）

14.8% on ImageNet2012

注意：貌似ALexNet和ZFNet现在很少有人用了（训练要好几周，而且没有可以用的预训练权重），而且这种经典架构可能完败于VGG（对深度很好的一次探索，具有良好的泛化性），在非经典架构可能也比不上Resnet和InceptionV3，所以这个结果的意义不是很大

## 5.2 图像模型尺寸（过时）

这个很多人探究过，比较主流的是全连接基本用不着（被全局平均池化所替代），最大池化的性能也有待商榷（可以用卷积层来代替）

而唯一重要的是特征的维度（这个还没看过相关论文）和网络的深度（深度必须要达到一个最小值，这个VGG探究过）

## 5.3 特征泛化性（这个很重要，但是作者的比较好像也有点过时）

作者和几个模型比较了一下，在Caltech-101和Caltech-256上的表现都较好（从头训练明显差得多），在Pascal VOC上的表现只能说有竞争力。。。

# 6 总结

本篇讨论的很多内容看起来已经有点过时了，毕竟只是看山之作。

**其中最重要的思想是Deconv，从特征映射到像素空间很重要，用于无监督学习、可视化还有上采样都可以**

另外作者对网络分析的思路也是蛮重要的：网络提取的特征是啥样的啊？？
可视化特征之后又有了很多思考：特征怎样去改进网络？？特征对训练有什么启发？？


  [1]: http://a2.qpic.cn/psb?/V14DGBA82xqcTT/LUqFW*N.zoefxwlf5YehjAJO1ftZKnDesVTWyCtR6*8!/b/dPcAAAAAAAAA&bo=IgQ1AQAAAAARByI!&rf=viewer_4
  [2]: http://a1.qpic.cn/psb?/V14DGBA82xqcTT/14mHBK18S9eHsds9f0jebXip3YZt5T7KHzrjM3Q50Ag!/b/dPkAAAAAAAAA&bo=3gPrAQAAAAARAAA!&rf=viewer_4
  [3]: http://a2.qpic.cn/psb?/V14DGBA82xqcTT/wL0UqdYaA7BYrMxvkqkcocnysDLZMAK9ep7QR05NQDo!/b/dB4BAAAAAAAA&bo=AwMqAQAAAAARBxs!&rf=viewer_4
