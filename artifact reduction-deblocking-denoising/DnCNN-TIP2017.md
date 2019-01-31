
# DnCNN-TIP2017 #

Beyond a Gaussian Denoiser,号称不仅仅适用于高斯白噪声，也可用于未知level的高斯白噪声。所以文中用同一个DnCnn同时训练几种任务：Blind Gaussian denoising（多level）, single image super-resolution（SISR ,降采样再上采样产生的噪声,多尺度） and JPEG image deblocking（多种质量系数）。文中也提及了， 很多深度学习去噪算法如MLP、TRND都是针对某level的噪声训练一个模型，不太通用。


DnCnn利用残差学习和批处理归一化加速训练过程，提高去噪性能。
如图网络结果比较简单，第一层使用conv+relu，然后接着d层的conv+batch normalization+relu,最后再一个conv层。conv使用3x3的核，最后一层卷积生成1通道的噪声残差，然后与输入相减得到去噪的图。其中每个卷积的特征图个数为64。

![](https://github.com/jlygit/AI-video-enhance/blob/master/artifact%20reduction-deblocking-denoising/image/QQ%E6%88%AA%E5%9B%BE20190131131042.jpg)

**实验部分**

DnCNN-S：高斯噪声level分为σ = 15,25 and 50三个模型，使用400张180x180的图片生成不同level的训练数据（实验中说大量的训练数据仅仅带来微弱的效果提升），其中图片分成40x40的块，batchs大小为128。
DnCNN-B：单个模型用于blind Gaussian denoising，噪声级别范围为σ ∈ [0, 55]，图片分成50x50的块，batchs大小为128。
DnCNN-3：单模型处理三种任务，包括高斯盲去噪、SISR、JPEG deblocking,盲去噪产生[0, 55]级别的数据集，SISR使用factors2，3，4的上下采样，deblocking使用quality factor为5 to 99 using the MATLAB
JPEG encoder。块大小为50x50，batchs大小为128。

看效果。
![]([https://github.com/jlygit/AI-video-enhance/blob/master/artifact%20reduction-deblocking-denoising/image/QQ%E6%88%AA%E5%9B%BE20190131152446.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/artifact%20reduction-deblocking-denoising/image/QQ%E6%88%AA%E5%9B%BE20190131152446.jpg))

## 开源实现 ##

**论文链接** [https://arxiv.org/pdf/1608.03981v1.pdf](https://arxiv.org/pdf/1608.03981v1.pdf)
**tensorflow**   [https://github.com/wbhu/DnCNN-tensorflow](https://github.com/wbhu/DnCNN-tensorflow)

**PyTorch** [https://github.com/SaoYan/DnCNN-PyTorch](https://github.com/SaoYan/DnCNN-PyTorch)

**keras** [https://github.com/husqin/DnCNN-keras](https://github.com/husqin/DnCNN-keras)

**matlab** [https://github.com/cszn/DnCNN](https://github.com/cszn/DnCNN)

