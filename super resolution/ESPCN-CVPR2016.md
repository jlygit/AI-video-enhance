# ESPCN #

we present the first convolutional neural network
(CNN) capable of real-time SR of 1080p videos on a single
K2 GPU.

作者声称K2 GPU上可以做到1080P视频的实时超分。

ESPCN也是最后一层才做上采样，意味着输入和特征映射等都是LR分辨率的大小，这样可以提升速度效率，并可以用更小的核覆盖更大的区域信息，从而更有可能达到实时。

文中说了，很多传统算法在第一层先将LR图像经过bicubic为HR图像作为CNN网络的输入，一方面加大了计算耗时和内存消耗，另一方面，经过bicubic后的LR图像并没有带来更好的用于超分学习的输入信息，甚至可能容易带来一些噪点。所以文中直接是端到端的LR到HR的CNN超分网络，通过网络学习LR到HR的直接映射关系，也就是所有细节、轮廓等信息都通过网络来学习。ESPCN通过L个层来学习输入图像LR的n个特征图，也更加容易学习到更复杂更好的LR到HR的映射关系，最后一层通过文中提出的亚像素卷积来重建超分图像HR。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181217210829.jpg)

文中将HR图像通过r倍的scale降采样为LR图像，然后经过高斯模糊做了处理。LR表示为H × W × C的张量，HR表示为rH × rW × C的张量。然后对C个通道（为了实时，文中仅用了图像的Y通道）的LR图像做了L层（L文中取3）的卷积学习，第一层的卷积核为5×5，输出特征图个数64，第二层的卷积核为3×3，输出特征图个数32，最，最后一层卷积核3×3，最后的输出特征图个数为C×r×r（这里假设超分2倍，r=2，C=1，那么输出的特征图个数为4个）。

对学习到的特征图，最后使用 Efficient sub-pixel convolution layer：亚像素卷积来重建HR图像，直接看下图，其实就是每个通道有r×r个特征图，然后r×r个特征图相同位置的所有像素点（r×r个像素）排列成长为r宽为r的单通道像素图，这就是亚像素重建。

本算法只考虑y通道的超分。
![效果](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181217214229.jpg)

## 开源实现 ##

**论文链接**  [https://arxiv.org/pdf/1609.05158.pdf](https://arxiv.org/pdf/1609.05158.pdf)

**tensorflow** [https://github.com/drakelevy/ESPCN-TensorFlow](https://github.com/drakelevy/ESPCN-TensorFlow) [https://github.com/kweisamx/TensorFlow-ESPCN](https://github.com/kweisamx/TensorFlow-ESPCN)

**PyTorch** [https://github.com/leftthomas/ESPCN](https://github.com/leftthomas/ESPCN)
**caffe** [https://github.com/wangxuewen99/Super-Resolution/tree/master/ESPCN](https://github.com/wangxuewen99/Super-Resolution/tree/master/ESPCN)

