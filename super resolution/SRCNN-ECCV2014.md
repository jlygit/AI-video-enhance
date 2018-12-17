# SRCNN #
SRCNN为首个使用CNN进行端到端的单图像超分的算法。算法对输入的低分辨率图片首先做一个前处理操作：使用传统算法bicubic将低分辨率图片超分到目标高分辨率的大小，称为Y，然后通过CNN学习到的一种映射F(Y)得到最后的超分结果。映射F的目的是尽可能地使Y接近ground truth。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181217172119.jpg)

F的设计主要包括了三个操作：

- Patch extraction and representation：特征学习，首先将Y分成很多固定大小的小块(patches)，然后通过卷积学习到特征向量，最后生成了n1个特征图，文中n1为64，卷积核大小为9*9。
- Non-linear mapping:特征图重新非线性映射，将上一层的特征图映射到另一个高维特征图(mapped vector)，最后得到n2个特征图，这里采用了1*1的卷积核，n2为32。
- Reconstruction:重建，将上层高维特征图重建生成一个高分辨率图像，也是通过通过卷积方式生成了c通道的图像，卷积核大小为5*5。
