Koch, C., & Ullman, S. (1985). Shifts in selective visual attention: towards the underlying neural circuitry. Human Neurobiology. https://doi.org/10.1016/j.imavis.2008.02.004

# 0 一点前言

**Yarbus1967年**的书是关于怎样检测EyeMovements（自己发明的仪器），以及通过记录下的眼动数据来分析眼动的规律，得出的比较重要的结论就是：
**1.很容易关注到人脸，而且是人脸上的眼睛和嘴巴；
2.长时间的观察人眼会做cycle；
3.任务极大地影响人眼轨迹**

而这篇文章是显著性检测的一个引领之作。作者通过生物学和心理学的一些研究，提出了selective visual attention的模型，salency map、WTA等概念正式开启了saliency prediction这一领域。

# 1 Selective Attention

psychophysical心理物理学和physiological生理学的证据表明灵长类动物和人类进化出了a specialized processing focus moving across the visual scene。**作者将这种现象称之为shift of selective visual attention，并通过neuron-like elements构建网络来建模。**

# 2 结构

![此处输入图片的描述][1]

## 2.1 大体结构

主要是根据边缘、颜色、方向纹理等特征生成feature maps，这些图表示着在视觉场景中一个位置的显著性；另外，这些图可能有不同的尺度。

得到feature maps后，如果观察者选择关注了某个位置，和这个位置有关的属性将会映射到一个更高、更抽象、非地形(???)的表示central representation.这也表征了皮层处理不同特征的层次性。

## 2.2 两种机制

### 2.2.1 Saliency Map
第一种就是特征图融合得到saliency map。

### 2.2.2 Selective mapping

有了显著性图，要确保每次只有一个最显著的位置映射到central representation，所有就有一个显著性的比较过程，叫做WTA，具体怎么实现的没有仔细看

## 2.3 Shifting the processing unit

当观察完一个点后，显著性图应该发生变化，才能转移到下一个点。

一种是Local，一个位置随着时间衰减；一种是Central，WTA会返回一个信号来抑制被采用的location。收敛时间，决定转移到下一个点的时间由两个location的位置决定。

## 2.4 转移规则

近邻关系，相似度准则

# 3 生物学的基础

名词太多，没怎么看懂。

# 4 我的想法

关于eye movement的过程

KU算是比较系统地讲解了Eye Movements的内部机理.

首先，人应该很快速地整体感知了图像的低层特征，比如图像的边缘纹理等，但是也根据人脑记忆的东西如人脸和文字这些高阶特征（简单地匹配？？？），然后根据这些生成了saliency map；saliency map较大的值就是人眼的fixation位置；根据显著性的值，可能还有距离以及相似性来不断选择下一个关注的区域

这样，如果不考虑过程中的语义的话，那么**一开始就是根据低层特征和人的一些先验知识(如人脸、文字，怎么解释？？？)快速得到显著性的位置[并行加序列化的搜索]**


  [1]: http://img0.ph.126.net/KQZONyO_SGSZXLrdsbv67w==/6632671953164019789.jpg
