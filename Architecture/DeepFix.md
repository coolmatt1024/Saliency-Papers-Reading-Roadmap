**Kruthiventi, S. S. S., Ayush, K., & Babu, R. V. (2015). DeepFix: A Fully Convolutional Neural Network for predicting Human Eye Fixations. arXiv Preprint, 1–11. https://doi.org/10.1109/TIP.2017.2710620**


# 1 粗读

这篇文章是首次利用FCN来做Saliency Map。

思路很简单，但是也有很多Trick在里面

选定VGG（个人觉得VGG比不上Inception V3，但从提取的特征来讲的话），
前面特征提取的部分基本不变，就是block5使用了一种hole kernel来增大recepetion field（感受野）（因为输入图片很大，1024），而原来的输入仅为224（这里还要再考虑一下）
然后加两个inception层，来结合不同层次的语义
最后加了个LBC层，来学习center-bias的特性（个人感觉这个思想很好，怎样把一些先验知识，CNN学不到的加入CNN联合训练，感觉有很大前景啊）

另外，关于训练，确实想得有点简单了。
要考虑权重初始化，数据集一般都很少，要考虑预训练的方法，有了初始权重，要考虑学习率，还要考虑训练轮次和好多训练策略；反正就算设计了合适的结构，如果没有这些训练trick的话，也是难以达到令人满意的效果的。。。


