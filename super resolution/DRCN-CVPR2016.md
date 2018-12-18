# DRCN #

和VDSR同一个作者，网络深度差不多，就是结构上采用了Recursive Neural Network递归神经网络。

作者认为，多数计算机视觉任务通过增加网络深度来扩大感受野，通常一个卷积a conv layer增加了参数量，然后接着一个池化a pool layer来丢弃像素信息。 然而，super-resolutionand denoising超分、去噪等图像重建问题是非常看重图像细节的，因此一般DL都不会使用池化层，那么纯粹增加卷积层指挥增加更多参数量，一方面，容易导致过拟合，所有需要更多的训练数据，另一方面huge参数使模型难以存储和恢复。

所以为了解决这些问题，DRCN使用了a deeply-recursive convolutional network递归卷积网络。通过多次重复应用相同的卷积层（而不会增加参数量了），网络感受野达到41* 41（其中SRCNN的是13* 13）。

当然，另一个问题梯度爆炸还是会有的，训练收敛很难达到，文中使用了两种方法解决：首先，所有递归都设计成有监督的方式，每个递归都进行一次HR预测的重建，然后所有递归结果求均值得到最后的HR预测图像。另外，像VDSR一样，使用了resnet残差思想来重建HR图像（在超分这种场景input与output之间高度相关时，残差尤其有效）。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181218151406.jpg)

看图所示，与VDSR类似，先进行传统插值放大到目标HR的分辨率大小，DRCN结构：首先是embedding network，两层卷积用于提取输入图像的特征图，然后是inference network，就是递归卷积结构了，所有递归层使用同一个参数权重W，最后是reconstruction network，重建输出HR。

看下面展开递归部分，三个图实属同理，其中H1到H<sub>D</sub>使用同样的权重W，然后每个递归层<sub>d</sub>都加上input进行局部重建HR（也就是每个递归都经过ReconNet），然后所有重建进行求和去均值来获得最后HR的输出（称为重建层共享所有递归的局部预测）。最后的loss也设计为D+1个输出（D个递归，1个最终输出）与ground true的MSE函数。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181218152442.jpg)

DRCN的主要特征：recursive-supervision and skipconnection，递归监督和残差跳连接

实验部分：文中使用了16个递归，所以大概输入到输出经过了20个卷积，所有卷积核大小为3* 3，训练图像以步长为21分割为41* 41大小的patchs，然后以64批量输入进行随机梯度训练。学习率初始化为0.01，然后每5次epochs进行一次除以10的衰减，最小为10^-6。Titan X GPU上训练用了6天，擦了。

直接上效果。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181218153657.jpg)


## 开源实现 ##

**论文链接** [https://cv.snu.ac.kr/research/DRCN/](https://cv.snu.ac.kr/research/DRCN/)

**tensorflow** [https://github.com/jiny2001/deeply-recursive-cnn-tf](https://github.com/jiny2001/deeply-recursive-cnn-tf)

