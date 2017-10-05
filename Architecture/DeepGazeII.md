# 1 出发点

Alexnet有点过时了。。。基于VGG（提供了更好的特征空间）出现了很多对手（SALICON，DeepFix和PDP）
训练也有点过时了。。。使用SALICON重新训练（新推出的显著性数据集，包含1w张训练图片），再在MIT1003上微调

先把AlexNet换成VGG,**保留VGG的特征不变**，在后面堆叠1x1的卷积（特征组合），仍然**使用对数函数的损失**

# 2 实现

## 2.1 架构
![此处输入图片的描述][1]

 1. 经过VGG，输出的特征通道数为5x512

the feature maps of a selection of layers (conv5 1, relu5 1, relu5 2, conv5 3, relu5 4; selected via random search) are rescaled and cropped to match an earlier layer (conv2 1 in our implementation). 

 2. 经过readout network

由4个1x1的卷积网络加一个Relu非线性（16,32,2,1）

 3. 后处理（作者提出了一种概率模型，对结果统一进行后处理）
 
主要是高斯模糊；
中心偏置通过加一个baseline的偏置的方式来实现
然后通过一个非线性函数

## 2.2 训练

### 2.2.1 损失函数

损失函数仍然使用一个对数似然函数；依赖于readout network参数和高斯模糊选择的核的大小。

### 2.2.2 优化方法

坚持使用L-BFGS，这里使用了SFO(Sum of Functions Optimizer)

Sohl-Dickstein, Jascha, Ben Poole, and Surya Ganguli (2013). “Fast large-scale optimization by unifying stochastic gradient and quasi-Newton methods”. In: CoRR abs/1311.2115. url: http://arxiv.org/abs/1311.2115.

### 2.2.3 训练步骤

![此处输入图片的描述][2]

 1. VGG上面训练好卷积层的权重
 2. 使用SALICON dataset(10000 images with pseudofixations from a mouse-contingent task)，被证明用来做预训练很有用
 降采样(2)，SFO,mini-batch=100

 **另外MIT1003数据集用来做Early Stop的标志：**
 至少运行20epochs才进行early stop的判断；如果连续三个epoch的性能低于前面五个epoch的性能则说明模型差不多训练好了，继续下去会过拟合；如果epoch达到800，训练停止
 
 数据加强的话只是resize到1024x768（或相反），然后下采样
 
 3. 使用MIT1003进行fine-tune

> DeepGaze I showed that overfitting to images is in fact a much larger problem than overfitting to subjects

**作者使用了k-fold交叉验证的思想（这里k=10），然后采用了model ensemble的策略（和上面一样的停止标准）**

降采样(2)，SFO,mini-batch=100

 4. 在MIT300上进行测试

  十个模型取平均

# 3 实验结果上

## 3.1 Information Gain Explained

## 3.2 MIT Saliency benchmark

AUC和sAUC都比其他两个模型略高

## 3.3 模型预测举例


## 3.4 减除实验

DeepGaze I

在Deep Gaze基础上加Readout Network，IG略微下降（本来就过拟合，现在更严重）

将Alex换成VGG，IG上升很多；

再加上Readout，IG略有提升

再在SALICON上预训练，就是现在的DeepGaze

**（这儿可以有很多思考，到底什么是重要的）**

# 4 结论
## 4.1 与DeepGaze I相比（见上面的减除实验）

 1. 使用了VGG19，有了更好的特征空间
 2. 1x1的卷积层叠加替代了简单的线性加权
 3. 训练策略很明智

## 4.2 与Deepfix，Salicon等方法相比

 1. 多层的特征融合
 2. 复杂的融合方式
 3. 损失函数（这个很重要啊）

为了实现这个，作者不惜放弃微调VGG（参数太多，数据量不够，1w张对微调VGG的作用不显著）

## 4.3 一些想法

作者的出发点其实还是挺简单的，从DeepGaze I（深度网络，多通道融合，中心偏置），到DeepGaze II（更强大的深度网络，更复杂的通道融合，更合理的训练方式）

循着这个思路，还是有很多可以改进的地方：

 - 更强大的深度网络（使用inception v3和resnet）
 - 更复杂的融合方式（现在本质上是只是用了高层特征，能不能借鉴Salient Object Detection那种循环网络）
 - 中心偏置（这个是CNN本质的问题，要改很难；但是可以像DeepFix那样加权啊）
 
 
 
  [1]: http://img1.ph.126.net/bpg95GVF7zh_C4DQvdudhw==/3080743620116406916.jpg
  [2]: http://img0.ph.126.net/mXXGQHBS9HDvB-PkFkn4yA==/6632578494677952360.jpg
