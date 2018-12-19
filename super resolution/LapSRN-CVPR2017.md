# LapSRN #
金字塔超分算法。特点：

- 不要求input做传统的bicubic插值，因此可以降低计算复杂度
- 使用Charbonnier损失函数来训练，可以获得很高质量的重建
- 单个前向推理可以实现多种scale倍数的超分预测

作者认为现在超分的方法存在几个问题：

- 很多算法预先使用bicubic将LR插值到目标HR的大小，然后再进行预测推理，这个前处理加大了不必要的计算损耗，输入大分辨率的图，也让推理更慢，传统插值也很可能带来视觉伪影。当然，有些算法使用deconvolution反卷积来重建HR，或使用sub-pixel convolution来重建HR，这些办法受限于网络容量所以并不能学习到input到output的很复杂的 映射关系。
- 一般算法使用l2范数来作为loss，因此很容易使预测变得模糊，这也是因为l2很难捕获HR图像块的underlying multi-modal distributions（底层多模态分布），导致学习到的预测相对人类视觉感官上比较平滑。
- 另外，现存方法对重建HR一般只做一次上采样操作，这让产生大倍数的超分重建非常困难（如8倍），此外，这些方法也不能产生多个分辨率的中间预测，所以要适应不同倍数超分的需求就要训练多个模型，使工程上落地更加困难。


LapSRN基于CNN级联的方式实现一个类金字塔支持多scale级别的的超分模型，每一个级别中进行一次X2的上采样，3个级别就可以达到X8的超分预测了。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181219144544.jpg)

1. 如图，第一级别中，输入图片经过几个卷积提取特征图，然后经过a transposed convolutional layer反卷积来上采样2倍得到X2的特征图，这里出现2个红色箭头。 
1. 向下箭头为一个卷积预测LR到HR的残差residuals，并与LR经过反卷积上采样的图相加得到这一级别的HR预测，此时得到X2的超分图。
1. 另一个向右箭头继续重复1的过程，继续提取特征，这也是下一个级别预测了。多个级别就组成了X4、X8的超分预测了。
2. 最后的loss函数使用Charbonnier损失函数来改善性能。看下图和看原文。
3. 每层上采样都使用一个事先初始化的the bilinear kernel 来做卷积。

![loss函数](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181219151026.jpg)

LapSRN对于X4超分没有直接上采样，而是采样级别方式，两次级联的X2上采样，这样得到的效果会更好。


**实验部分**
文中每个卷积层使用3* 3的核，生成64个特征图。 反卷积核初始化为4*4 的 a bilinear filter双线性滤波器。每个卷积或反卷积后面紧跟着leaky rectified linear units (LReLUs) with a negative slope of 0.2。

训练集部分使用64个128* 128的小patchs作为一组随机梯度优化学习。然后训练集做了Scaling缩放、Rotation旋转、Flipping翻转等。

总体看，LapSRNA通过级联上采样，逐级预测残差的方式，在做高倍超分也能输出中间的超分预测。逐级上采样使得其速度更快，每一级别都有loss监督使得其效果更佳。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181219151924.jpg)

## 开源实现 ##

**论文链接** [http://vllab.ucmerced.edu/wlai24/LapSRN/](http://vllab.ucmerced.edu/wlai24/LapSRN/)

**tensorflow** [https://github.com/zjuela/LapSRN-tensorflow](https://github.com/zjuela/LapSRN-tensorflow) 

**pytorch** [https://github.com/twtygqyy/pytorch-LapSRN](https://github.com/twtygqyy/pytorch-LapSRN)

