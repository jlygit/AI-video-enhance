# SRGAN #

文中提出未解决问题：: how do we recover the finer texture details
when we super-resolve at large upscaling factors？ 精确度和速度在超分上都已经有很大突破，但是传统办法使用MSE作为loss函数（MSE正相关psnr，得到的psnr虽然很高），最后预测的结果容易丢失高频细节，导致比较平滑，不符合视觉感受。

The ill-posed nature of the underdetermined SR problem
is particularly pronounced for high upscaling factors, for
which texture detail in the reconstructed SR images is
typically absent.

SR的主要问题，尤其在高倍上采样时，出现高频纹理细节缺失。

所以文中引入如GAN网络到图像超分上，并使用了perceptual
loss感知损失（由对抗损失adversarial loss加上内容损失content loss），SRGAN也是首个X4超分可以达到逼真自然状态的框架，MOS值高哈。

GAN，简单就是通过生成网络loss与对抗网络loss的设计上同时追求损失最小，那生成网络极力生成一个内容接近ground truth的图片，对抗网络极力判别出来生成的图片是假图。这样相互对抗，最后达到一个平衡，不分伯仲的状态。

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220111456.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220111456.jpg)

如图，SRGAN的生成网络是一个resnet（称为SRResNet），content loss使用基于VGG框架的特征图计算的loss（对像素空间的变化更稳定）。图所示，生成网络G使用了B个残差blocks，每个block包括3*3 卷积核，生成64个特征图，后接着batch-normalization layers and ParametricReLU，然后再重复一次刚才操作，接着加上input，经过B个blockS后，做上几个卷积就，最后上采样使用已经训练好的sub-pixel convolution层，然后卷积生成HR图像。
 

对抗网络D使用8个卷积层，每层卷积核3* 3，第一层生成特征图个数64，后面层依次*2，最后一层生成215个特征图。然后接着两个dense层和一个sigmoid激活得到最后的分类概率。

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220165055.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220165055.jpg)

如图，损失函数的设计为content loss加上adversarial loss，content loss可选，为直接计算的MSE，或是经VGG-19框架提取特征后的某一层的MSE；

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220165322.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220165322.jpg)

adversarial loss为G预测后的结果输入到D对抗后的概率输出的-log（概率输出追求最大，所以加上-作为loss）。

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220165343.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220165343.jpg)

**实验部分**

SRGAN使用ImageNet的随机350k张图片作为训练集。LR是通过HR做4倍的下采样获得的，每一批输入为16个96* 96的HR子patchs。

不用对抗网络的生成网络为SRResNet，也做了实验，也分别对MSE和VGG loss做了实验：SRResNet-MSE, SRResNet-VGG22, SRGAN-MSE
, SRGAN-VGG22
, SRGAN-VGG54，其中SRGAN-VGG54结合的mos值是最好的，也就是视觉效果最好了（虽然psnr，ssim比其他会有降低）。

对于感知loss文中做了比较，直接mse来获得loss，使用VGG22获得低层特征求loss，使用vgg54获得高层特征获得loss，趋势是高频细节可以恢复得越来越好（表现为mos值提升）。如图所示：

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220170523.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220170523.jpg)


直接上SRGAN效果图，细节很丰满（虽然有些细节和ground truth不一致）。x4超分能在高频细节达到这个效果真是令人震撼。

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220170857.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181220170857.jpg)

## 开源实现 ##

**论文链接** [https://arxiv.org/pdf/1609.04802v2.pdf](https://arxiv.org/pdf/1609.04802v2.pdf)

**tensorflow** [https://github.com/brade31919/SRGAN-tensorflow](https://github.com/brade31919/SRGAN-tensorflow)    [https://github.com/tensorlayer/srgan](https://github.com/tensorlayer/srgan)


**caffe**  [https://github.com/ShenghaiRong/caffe_srgan](https://github.com/ShenghaiRong/caffe_srgan)


**PyTorch** [https://github.com/aitorzip/PyTorch-SRGAN](https://github.com/aitorzip/PyTorch-SRGAN)

**Keras** [https://github.com/titu1994/Super-Resolution-using-Generative-Adversarial-Networks](https://github.com/titu1994/Super-Resolution-using-Generative-Adversarial-Networks)

