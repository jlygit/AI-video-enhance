# VDSR #

作者称为高精度非常深的网络，受启发与VGG-net，VDSR一共用了20层网络，通过很多层的小卷积核作用是可以搜索到更大的上下文关系、感受野一种方法。而深层网络缺点是梯度弥散、梯度消失、不容易收敛，文中为了解决收敛问题，使用了残差结构resnet，仅仅学习图像的residuals残差，残差是目标图与原图的差值，更容易被学习到，并使用很大的学习率，VDSR提供用户自定义的scale超分倍数。


利用残差好处：很多网络如 SRCNN都是通过学习到的特征图来重建所有HR的细节，这样如在深层网络下the vanishing/exploding gradients problem渐变消失/爆炸问题就很严重了。所以文中旨在用残差解决这个问题。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181218110410.jpg)

如图，VDSR很简单粗暴，首先LR通过常规插值超分到目标HR的大小，称为ILR，然后直接做d层卷积操作，其中第一层做3*3卷积操作，产生64个特征图，接着中间做d-2层的3*3的卷积，然后最后一层做3*3的卷积产生3通道残差图，最后与ILR相加输出HR 预测图。

这里深层网络训练过程也会更慢，如果学习率很小，如10^-5，那VDSR至少要在GPU上 训练一周，所以加High Learning Rates大学习率是有助于训练上的收敛的，但是也容易引起vanishing/exploding gradients问题，所以文中建议使用大学习率时结合Adjustable Gradient Clipping（常用于递归神经网络）来遏制梯度爆炸。如下图可以看到大学习率的收敛性能。

[https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181218110516.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181218110516.jpg)

总结下，VDSR主要特征有：网络更深性能更好、残差学习、单模型多scale多种倍数的超分。所以性能是棒棒的，如图所示：

[https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181218113624.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181218113624.jpg)

## 开源实现 ##

**论文链接** [https://cv.snu.ac.kr/research/VDSR/](https://cv.snu.ac.kr/research/VDSR/)

**tensorflow** [https://github.com/Jongchan/tensorflow-vdsr](https://github.com/Jongchan/tensorflow-vdsr)   [https://github.com/kweisamx/TensorFlow-VDSR](https://github.com/kweisamx/TensorFlow-VDSR)

**PyTorch** [https://github.com/twtygqyy/pytorch-vdsr](https://github.com/twtygqyy/pytorch-vdsr)

**caffe** [https://github.com/huangzehao/caffe-vdsr](https://github.com/huangzehao/caffe-vdsr)
