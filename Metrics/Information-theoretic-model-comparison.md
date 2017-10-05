> Kümmerer, M., Wallis, T. S. A., & Bethge, M. (2015).
> Information-theoretic model comparison unifies saliency metrics.
> Proceedings of the National Academy of Sciences, 112(52), 16054–16059.
> https://doi.org/10.1073/pnas.1510393112

# 0 一点概念的说明(很重要，且有一定的启发性)

> If we are interested in understanding **naturalistic eye movement behavior**, free viewing static images is not the most representative condition (14–18). Understanding image-based fixation behavior is **not only a question of “where?”, but of “when?” and “in what order?”**. It is the spatiotemporal pattern of fixation selection that is increasingly of interest to the field, rather than purely spatial predictions of fixation locations. 

> Accounting for the entirety of human eye movement behavior in naturalistic settings will require incorporating information about **the task**, **high-level scene properties**, and **mechanistic constraints on the eye movement system.**
> 
> Successful image-based fixation prediction models will therefore need to use such higher-level features, combined with task-relevant biases,to explain how image features are associated with the spatial distribution of fixations over scenes.

首先，眼动行为是指人眼看到图片时的运动轨迹（eye tracking movement、human gaze placement，包括where、how long和what order，个人觉得when不太合适）

现在的重点方向是where（predict where human look in images），称作fixation prediction（有时也被称作saliency prediction，但我觉得saliency又不太一样）。虽然有eye tracking data，但是用更多的还是fixation locations(所有人的关注点)和fixation map(最常用，也称作saliency map，衡量的是关注点的密度)

顺带提一句，saliency object detection和fixation predictions的出发点相同，但是一个侧重目标，一个侧重位置

# 1 出发点

 - Eye movement prediction很重要、关于它的研究很多
 - 在计算机视觉方面saliency prediction已经很成熟（bottom-up和top-down）
 - 现在的指标没有很好的一致性（他们对saliency map的定义不一样）

思路：不同的人看同一幅图像有不同的结果；同一个人多次看同一幅图像也有不同的结果；显然，这是一个概率问题。从信息论的角度去衡量，就是我们从模型预测到的saliency map，相比prior先验，去掉了多少不确定性。

# 2 理论介绍

## 2.1 信息增益

如掷骰子，我们期待的顺序为1,2,3,4,5(ground thuth),得到的顺序为2,1,3,4,5（model prediction），而不加控制得到的顺序为1,1,1,1,1（baseline prediction）

现在的问题就是衡量我们得到的顺序有多好，一种方法就是直接比较期待的顺序和得到的顺序的相似程度（KL散度、Similarity等），另一种方法就是我们和baseline比较，看比baseline好多少（消除了多少不确定性）

## 2.2 衡量模型评价指标

将各个模型指标的相对性能画出来，可以发现模型不具有一致性(比较相似形也可以看出)

将IG作为横轴，模型相对性能作为纵轴，发现模型和IG之间也不具有一致性

将各个模型统一为概率模型（高斯模糊，非线性，中心偏置）之后，在衡量各个指标，发现具有很好的正相关（虽然仍然不是对角线）

**PS：作者的意思应该是把模型统一经过这样的处理可以得到很好的一致性吧（？？？）**

## 2.3 基于像素空间的信息增益

作者提到，很多指标如AUC不能在像素级别进行评估（要在图像或者数据集上才能评估）；但是将模型归一化到概率空间，可以比较模型失败在哪儿和失败了多少。

![此处输入图片的描述][1]


![此处输入图片的描述][2]

 1. 模型预测得到density（密度）
 2. 除以先验prior(就是所谓的baseline)得到image-based prediction(得到的分布和先验分布的差异性)
 3. 取对数，转换为information gain
 4. 和golden map的信息增益做差，得到information Gain差异图，从这个图可以看出模型哪里预测的不好，不好多少

> 如果从最后的结果看，可以推导，最后得到的就是Pgolden和Pmodel之间的KL散度，就是用来衡量两个分布之间的差异性的；

更具体一点的解释：
![此处输入图片的描述][3]

在这幅图中，蓝色表示高估了fixation密度，红色表示低估了fixation 密度，黄色表示预测的刚刚好

作者强调可解释的信息增益为
![此处输入图片的描述][4]

实验细节

> The baseline model is a 2D histogram model with a uniform regularization (to avoid zero bin counts) cross-validated between images (trained on all fixations for all observers on all other images)

好像就是一个统计信息，每个位置上fixation的密度，不过是以区域划分的

> This is our gold standard model. It was created by blurring the fixations with a Gaussian kernel and including a multiplicative center bias (Phrasing Saliency Maps Probabilistically), learned by leave-one-out cross-validation between sub-jects.


# 总结


## 作者的想法
衡量Saliency Model的好坏不像分类、检测那么简单，也不想分割那样可以计量。

我们得到了model预测的Saliency Map，怎样判断和ground truth有多相近（近的定义是什么）呢？？？

像AUC、SAUC这种只是为了衡量一个检测器的好坏（本质上是二值化的）
像Similarity（最小值求和）是同一位置像素越接近相似度越高
像KL散度是两种分布越相近越好
还有NSS等指标

**这些指标都没有很强的说服力，而且之间也不是一致的（Pearson和Spearman相关系数），通过将密度图转换为概率图之后，再用这些指标衡量，一致性就比较好了（这是文章的题目，也是作者主要想强调的点）**

**作者还提出了explained information gain这个概念，我们希望模型消除的不确定度和ground truth消除的不确定度应该尽可能相同**

## 我的想法

从信息论出发是一个很好的出发点（prediction确实是一个概率问题，其实分类也是有一定概率在的），将模型统一化为概率图确实是一个不错的idea，同意了指标

另外，损失函数对结果影响比较大啊，就像当初AE自编码器用Euc loss和交叉熵损失差别就比较大


 


  [1]: http://img1.ph.126.net/Q_WDMi7VGBz0fel3dLN3Jg==/6632567499561731902.jpg
  [2]: http://img2.ph.126.net/i7nZy3q_MBaxzp2Z3dSYVg==/6632399274282679526.jpg
  [3]: http://img0.ph.126.net/Q7pQ7Y6lEvZufca-XMEMCw==/6632292621654797187.jpg
  [4]: http://img2.ph.126.net/bEvUE1OiWLx0o2xzJ-J9_Q==/92323792379437340.jpg
