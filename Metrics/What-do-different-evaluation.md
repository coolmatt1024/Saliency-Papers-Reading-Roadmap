> Bylinskii, Z., Judd, T., Oliva, A., Torralba, A., & Durand, F. (2016).
> What do different evaluation metrics tell us about saliency models?,1–24. Retrieved from http://arxiv.org/abs/1604.03605


# 0 引言



> Automatically predicting regions of high saliency in an image is useful for applications including content-aware image re-targeting, image compression and progressive transmission, object and motion detection, image retrieval and matching. Where human observers look in images is often used as a ground truth estimate of image saliency, and computational models producing a saliency value at each pixel of an image are referred to as saliency models.

# 1 指标的选择应该考虑什么？？

输入图像应该是什么样的？
ground truth怎么来得到啊？
眼动如何来呈现？？？

## 1.1 数据收集

图像：300 natural images（1024x？）

观察方式：**free-view**，memory task，object annotation等，其中free-viewing是假设最少的方式

观察时间：3s（MIT300，**大约4-6fixations**）、5s（CAT2000）等；

受很多其他因素的影响（图像大小，距离，观察人数等）

**dva(one degree of visual angle) is approximately 35 pixels**

dva衡量了实验者在fixation期间关注的图像区域

## 1.2 Ground Truth表示(unfinished)

 - fixation locations离散的关注点(个体差异很大，相当于fixation map的采样)
 - fixation map通过高斯（one degree of visual angel）平滑加权得到分布密度（可能会影响指标的评价）
 - **另外，评估fixation sequence的指标也被人提出过，但是序列一般噪声多而且难以评定，所以大多数显著性模型都只是针对位置预测**

## 1.3 指标计算

作者将指标分为两种，一种是将ground truth看作离散的点，另一种是看作连续的fixation map

## 1.3.1 Location-based metrics

### 1. AUC（Area Under Curve）

在信号检测领域，ROC曲线常用作评价一个分类器的标准

TP是指正确的样本分为正确的；TPR（TP over T）
FP是值错误的样本分为正确的；FPR（FP over F）

**TPR是指门限以上的location over 总的fixation**

对于Judd的方法,将整幅图中fixation以外的看做unfixation，FPR就是指门限以上的unfixation over 所有的unfixation[更被提倡]

对于Borji的方法，FP是随机选的样本（看来不区分fixation）作为负样本，FPR就指门限以上的unfixation over 所有的unfixation

### 2. sAUC(shuffled AUC)

**TPR是指门限以上的location over 总的fixation**

FP是根据其他图像的fixation locations来确定的，具有中心特性

**对于加了中心偏置的图像，阈值高，中心区域会更大，那么FP就也会升高，是的sAUC的值也会降低。所以sAUC可以惩罚中心偏置，或者说sAUC对中心偏置具有不变性**

### 3. NSS(Normalized Scanpath Saliency)[被推荐]

首先将得到的fixation map map去均值归一化，然后在fixation的位置的值相加取平均

### 4. IG（Informaation Gain）

NSS是希望每一个fixation预测得到的值尽量大，其他位置的值尽量小

而IG是希望在每个fixation得到的值尽量大（用概率或者信息熵来衡量）
 
 **IG可以用来比较不同模型，也可以用来衡量单个模型（给定baseline还有goldenline，goldline就是用fixation map表示的ground truth）**
 
## 1.3.2 Distribution-based metrics
说白了就是两个fixation map的相似性（图像的相似性）

### 1. SIM(Similarity)

> a metric for color-based and content-based image matching,it has gained popularity in the saliency community as a simple comparison between pairs of saliency maps

这个以前见过，因为只取小值，所以两幅图像越相似，得到的值也会越高

但是，显然模糊对图像的影响很大，模糊使绝对值减小

### 2. CC（Pearson‘s Correlation Coefficient）

> a statistical method used generally in the sciences for measuring how correlated or dependent two variables are.

将得到的fixation map看成随机的数，计算相关系数（相乘 over 总平方和开根号）

### 3. KL散度

看成概率分布，比较两个概率分布的相似性（对于o概率，用一个极小值来代替）

### 4. EMD(Earth Mover's Distance)

> The Earth Mover’s Distance, EMD, measures the spatial distance between two probability distributions over a region. It was introduced as a spatially robust metric for image matching.Computationally, it is the minimum cost of morphing one distribution into the other. 


> A larger EMD indicates a larger difference between two distributions while an EMD of zero indicates that two distributions are the same. 

# 2 总结

这篇文章重要的几个地方：

**指出了两种ground truth及其衡量指标（还简单提了一下时间的）**

**讲了几种评价指标，感觉还蛮有用的**


另外，作者提到了设计指标的一些指导思想，还是蛮有用的；最后给出了一些推荐指标，如果是概率模型可以用KL散度或者IG；如果想包括系统偏置，可以使用NSS或者CC
