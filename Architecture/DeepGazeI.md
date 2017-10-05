> Kümmerer, M., Theis, L., & Bethge, M. (2014). Deep Gaze I-Boosting Saliency Prediction with Feature Maps Trained on ImageNet. arXiv:1411.1045 [Cs, Q-Bio, Stat], (2014), 1–11. Retrieved from http://arxiv.org/abs/1411.1045%5Cnfiles/1004/arXiv-Kummerer_et_al-2014-Deep_Gaze_I-Boosting_Saliency_Prediction_with_Feature_Maps_Trained_on_ImageNet.pdf

# 1 出发点

现在Saliency Prediction还没能包含特别高阶的特征，深度学习提取特征的能力无敌强，而且迁移能力还贼好

> a network trained for some task like object detection can often be easily retrained to achieve state-of-the-art performance in some other only loosely related task like scene recognition. 

eDN用了一个简单的三层网络，就取得了state-of-art的结果（主要还是首先受限于数据集，不然会有更复杂的网络），所以作者结合分类网络和迁移学习，提出了Deep Gaze I

# 2 实现

## 2.1 架构


![此处输入图片的描述][1]

 
 - 初始的降采样（可能是为了降低运算量）
 - 高层特征和底层特征的融合。
 - 通过一个高斯滤波器（可能是平滑的更像Saliency Map吧）
 - 加一个中心偏置，经过softmax，输出预测的Saliency Map

损失函数使用对数似然函数(具体可以看作者提出的用信息论评估显著性的文章)

## 2.2 训练

### 2.2.1 训练数据

**Training： MIT1003，462张（为了用中心偏置的非参估计），1024x768
Test：MIT1003,540张，1024x768**

### 2.2.2 网络架构

 - 不需要固定的输入 
 - 需要padding来保持大小输入不变
 - caffe中的架构和Alexnet略有不同
 - 所有的计算是通过theano来完成的

### 2.2.3 训练方法

#### 2.2.3.1 优化方法

**the mini-batch based BFGS method**

> It combines the benefits of batch based methods with the advantage of second order methods, yielding high convergence rates with next to no hyperparameter tuning.

> CS231n中提到了这种方法，本质上是二阶近似（Hessian矩阵的近似）；但是这种方法Usually works very well in full batch, deterministic mode；Does not transfer very well to mini-batch setting。不知道作者为什么会使用这种方法

#### 2.2.3.2 超参数

格点搜索

关于超参数，一般使用从粗糙到精细的交叉验证策略

> **First stage:** only a few epochs to get rough idea of what params work
> **Second stage:** longer running time, finer search … (repeat as
> necessary)

学习率一般就是固定格点搜索的方式

#### 2.2.3.3 验证

在15个目标上进行**leave-one-out留一交叉验证**

#### 2.2.3.4 训练过程

载入ALexnet预训练的权重
用MIT1003数据集来微调（只用了463张图片来做训练）

# 3 结果

## 3.1 Information Gain(Unfinished)

![此处输入图片的描述][2]

DeepGaze I可以达到56%的信息增益

## 3.2 MIT Saliency Benchmark上的结果
![此处输入图片的描述][3]

碾压……AUC和SROC有了蛮大的提高

## 3.3 层的选择

在测试集上，只是用layer5卷积层时，sROC有最好的结果（layer5不变性最好）

当使用all layers时，IG很高，泛化的sROC却不高。

## 3.4 特征分析（显著性得到了哪样的特征呢？）

![此处输入图片的描述][4]
作者选了256层中权重最大的十个层，再从中选出产生最大相应的九个块（就是固定一个feature map，然后从输入图片中找出最大相应的图像块）

可以看出，人脸、文字还有一些pop out的部分（作者说是更抽象的，我觉得反而可能是底层特征）对显著性影响比较大



# 4 总结

迁移学习的强大


当然，迁移学习已经向我们证明了它的强大

> a network trained for some task like object detection can often be easily retrained to achieve state-of-the-art performance in some other only loosely related task like scene recognition.

从一些密切相关的检测、分割任务，到现在的saliency map和saliency object detection，不得不说，分类网络很大程度上和人脑契合，可能还是有一些改进的地方，但在特征提取上能力强大



  [1]: http://img2.ph.126.net/ZxosWk5SaBJjmbrRg8tEoA==/6632379483073299095.jpg
  [2]: http://img2.ph.126.net/R2TBY3oH3asAay2Ze3D_wg==/6632445453770994974.jpg
  [3]: http://img1.ph.126.net/pBVy9vxSLEpxHiBnpUOIAA==/6632258536794273031.jpg
  [4]: http://img0.ph.126.net/jTt2-E9Xi7zybacJjG0r-A==/6632757715070686182.jpg
