# EDSR #

又一个resnet的超分结构，NTIRE2017的冠军作品。

based on the SRResNet architecture,
we first optimize it by analyzing and removing
unnecessary modules to simplify the network architecture. 是基于SRResNet（SRGAN的生成网络）的优化，移除了一些不必要的结构，简化Residual blocks结构。

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220175338.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220175338.jpg)

如图，原始block是经过卷积、批量归一化（BN）、激活ReLU、卷积、BN，然后加上输入并最后激活得到输出；SRResNet的block去掉了最后一个激活，EDSR则去掉了所有BN，所以网络结构更简洁（特别BN耗内存也耗时），这样就可以增加更多的层来训练更复杂的网络。

EDSR整个网络结构如下图所示，整个结构基于SRResNet，相比之下，ResBlock去除了BN层，并且block的特征数相同，如64或256，一共指定了32个blocks。

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220190202.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220190202.jpg)

看结构，是LR输入一个conv提取特征，然后B个resblock提取全局的高层特征，然后一个conv后加上低层特征作为特征的学习结果，然后上采样后，进行一次conv得到HR图像，其中上采样支持X2、X3和X4等倍数（另外，文中也提供了MDSR多倍数超分的结构）。


**实验部分**

使用了最近剔除的2K图DIV2K数据集，然后将图像裁剪为48* 48的RGB通道的LR图像，同时进行翻转和旋转扩展数据集（注意，是使用了RGB三通道，不是Y通道而已了）。loss函数使用了L1函数。

结果又多屌直接看图

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220192743.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220192743.jpg)

## 开源实现 ##

**论文链接** [https://arxiv.org/abs/1707.02921](https://arxiv.org/abs/1707.02921)

**tensorflow** [https://github.com/jmiller656/EDSR-Tensorflow](https://github.com/jmiller656/EDSR-Tensorflow)


**PyTorch** [https://github.com/thstkdgus35/EDSR-PyTorch](https://github.com/thstkdgus35/EDSR-PyTorch)

**Torch ** [https://github.com/LimBee/NTIRE2017](https://github.com/LimBee/NTIRE2017)

