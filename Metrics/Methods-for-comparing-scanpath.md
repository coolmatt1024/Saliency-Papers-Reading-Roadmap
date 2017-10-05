> Le Meur, O., & Baccino, T. (2013). Methods for comparing scanpaths and saliency maps: strengths and weaknesses. Behavior Research Methods, 45(1), 251–266. https://doi.org/10.3758/s13428-012-0226-9

# 1 引言

主要就谈一下scanpath这方面

> Visual scanpaths depend on bottom-up and top-down factors such as the task users are asked to perform (Simola, Salojärvi, & Kojo, 2008), the nature of the stimuli (Yarbus, 1967), and the intrinsic variability of subjects (Viviani, 1990).

**能够比较两个可视行为的差异的意义**

 - 区分不同因素的影响
 - 理解什么主导了我们的认知过程
 - 评估模型性能（如评估基于显著性的模型能多好地预测where human looks）

# 2 比较scanpath的方法

## 2.1 String edit metric

这个方法先把图像化成不同的区域（AOIs）[作者举的是广告的例子，分为上、左、右、中上、中下五个区域]，然后根据fixation落在哪个区域对应得到一个序列

两个序列的距离使用的是Levenshtein距离

**问题：AOI怎么来定义？？？**

> 可以将人眼视觉考虑进去（35 pixels那么划分？？？），还有就是考虑字母之间的距离（要考虑空间距离的相似性）

## 2.2 Mannan's metric

D表示两个scanpaths的距离，Dr表示两个任意scanpath的距离

距离的计算使用最邻近法

计算一条路径A到另一条路径B的距离AB并归一化（找最近邻点），同样计算BA, AB+BA加起来就是两条路径的距离

然是如果A有很多点但是聚集在一个位置，B只有两个点（一个靠近A，一个离A很远），这样计算出来的距离很大

**缺点：没考虑时间特性；对两canpaths的变化比较敏感**

## 2.3 Vector-based metric

对scanpath向量化（两个fixation之间的距离和方向）

> Once the scanpaths are aligned, various measures of similarity between vectors (or sequences of vectors) can be used, such as average difference in amplitude, average distance between fixations, and average difference in duration.


优点：
> it can align scanpaths not only on the spatial dimension, but also on any dimension available in saccade vectors (angle, duration, length, etc.).

# 3 a realistic upper bound（unfinished）

像AUC这种，如果能够达到一个人的水平就已经有很不错的性能了

**IOC(inter observer congruency观察者一致性)取决于几个要素**

**IOC可以通过one-against-all来衡量**

1. stimulus开始的时候一致性很好，随着时间逐渐降低

> Tatler, B. W., Baddeley, R. J., & Gilchrist, I. D. (2005). Visual corre- lates of fixation selection: Effects of scale and time. Vision Re- search, 45, 643–659.

刺激开始的时候，注意力主要是受底层特征影响，然而several seconds之后top-down机制越来越重要

2. visual content itsels

没有pop out的时候，IOC很小；有显著性的区域的时候有很高的一致性


> A number of studies have shown that we are able to identify and recognize human faces and animals very quickly in a natural scene (Delorme, Richard, & Fabre-Thorpe, 2010; Rousselet, Macé, & Fabre-Thorpe, 2003).


> Althoff and Cohen’s(1999) study is a good example of this point. They investigated the effect of memory or prior experience on eye movements.

3. cultural differences

 

# 4 fixation的含义是一样的吗？

> Fixations differ in both their durations and their saccade amplitudes during real-world scene viewing.

**作者根据saccade将fixation分成两种Focal processing持续时间长，扫视比较短；而Ambient processing持续时间短，扫视时间长**
