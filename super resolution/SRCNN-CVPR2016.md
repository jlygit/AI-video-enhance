# ESPCN #

we present the first convolutional neural network
(CNN) capable of real-time SR of 1080p videos on a single
K2 GPU.

作者声称GPU上可以做到1080P视频的实时超分。
ESPCN也是最后一层才做上采样，意味着输入和特征映射等是LR分辨率的大小，提升速度效率，并可以用更小的核覆盖更大的区域信息，从而有可能达到实时。文中说了，很多传统算法在第一层先将LR图像bicubic为HR图像作为CNN网络的输入，一方面加大了计算耗时和内存消耗，另一方面，bicubic后的LR图像并没有带来更好用于超分学习的输入信息，甚至带来一些噪点。还有的是，通过L个层来学习输入图像的n个特征图，可以学习到更复杂更好的LR到HR的映射关系。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181217210829.jpg)

文中将HR图像通过r被的scale降采样为LR图像，然后经过高斯模糊做了处理。LR表示为H × W × C的张量，HR表示为rH × rW × C的张量。然后对C通道的LR图像做了3层的卷积学习，第一层卷积核为5*5，输出特征图个数64，第二次卷积核3*3，输出特征图个数32，最，最后一层卷积核3*3，输出特征图个数为C×r×r。

对学习到的特征图，最后使用 Efficient sub-pixel convolution layer，亚像素卷积来重建HR图像，直接看图，其实就是每个通道有r*r个特征图，然后特征图相同位置的r*r个像素排列成长r宽r的单通道像素图，这就是亚像素重建。

本算法只考虑y通道的超分。
![效果](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181217214229.jpg)

## 开源实现 ##

**论文链接**  [https://arxiv.org/pdf/1609.05158.pdf](https://arxiv.org/pdf/1609.05158.pdf)

**tensorflow** [https://github.com/drakelevy/ESPCN-TensorFlow](https://github.com/drakelevy/ESPCN-TensorFlow) [https://github.com/kweisamx/TensorFlow-ESPCN](https://github.com/kweisamx/TensorFlow-ESPCN)

**PyTorch** [https://github.com/leftthomas/ESPCN](https://github.com/leftthomas/ESPCN)

**caffe** [https://github.com/wangxuewen99/Super-Resolution/tree/master/ESPCN](https://github.com/wangxuewen99/Super-Resolution/tree/master/ESPCN)

