# FSRCNN #

FSRCNN是对SRCNN的一个改进版，速度更快，性能更好。FSRCNN与SRCNN都是香港中文大学Dong Chao等人提出的。

1.不需要像SRCNN那样经过bicubic前处理成目标分辨率的大小，可以直接原图LR卷积生成特征图，然后在最后一层通过反卷积层直接将低分辨率特征图生成高分辨率的超分结果图HR。
2.将中间maping层前面加入shrinking和后面加入expanding，可以减少需要进行maping的特征维度，降低运算量。
3.增加了mapping的层数，减少卷积核大小，这里因为特征图大小是LR大小，更小的卷积核就可以达到SRCNN那种9*9卷积核覆盖到的信息，并且通过多层mapping学习更加复杂的特征图之间的映射关系。

FSRCNN相对于SRCNN速度上提升了40倍左右，并且超分质量也得到保障，号称是CPU下可以达到实时速度的（24fps）。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181217195042.jpg)

图中可以看出三个主要改变：

- 原始小图直接输入，不用bicubic处理。然后是特征提取层，SRCNN采用了9*9的核，因为其输入是通过bicubic的处理，所以卷积核比较大（9*9大小），在FSRCNN就采用了5*5的核，图像输入通道为1（Y通道），输出特征图个数为d个。
- 非线性mapping改为shringing层、多层mapping、expanding层组成。改进SRCNN中mapping特征通道个数过大的缺点（导致运算量大），FSRCNN首先使用1*1的卷积做特征线性融合处理，输出s个通道的特征图，s远小于d，这样相当于降低特征维度；然后经过多个非线性mapping的映射学习保证质量，这里用了m个3*3的卷积层来mapping，输出同样为s通道的特征图，最后通过1*1的卷积做expanding处理，生成了d通道特征图（其实是shringing的逆运算，做特征图个数的恢复），据文中提到可以提升0.3db的psnr指标。
- 最后一层采用反卷积层重建高分辨率图像，可以很方便指定多种倍数的超分（X2、X3、X4等），这里使用的卷积核为9*9。
- 另外FSRCNN使用了PRelu激活函数，不同于SRCNN使用的Relu激活函数f(x<sub>i)=max(x<sub>i,0)，PRelu定义为f(x<sub>i)=max(x<sub>i,0)+a<sub>i*min(0,x<sub>i)，ai用于当xi为负数的情况下。PRelu主要避免了Relu中0梯度引起的“dead features”，可以充分利用所有网络参数。

## 训练与实验 ##
文中对训练集合做了一些扩张，首先提出了一个新的数据集，包含100张轮廓清晰、少平滑区域（如天空、大海）的适合超分训练的图像。然后还通过scaling（0.6-0.9）、rotation（90-270度）手段来扩充数据集。
这里将原清晰大图下采样为指定倍数n的小图，然后再分割为很多块patchs，用于与对应的清晰ground truth组合pairs作为训练集。

![不同参数下的性能表现](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ截图20181218103827.png)

## 开源实现 ##

**算法主页**  [http://mmlab.ie.cuhk.edu.hk/projects/FSRCNN.html](http://mmlab.ie.cuhk.edu.hk/projects/FSRCNN.html)

**tensorflow**  [https://github.com/yifanw90/FSRCNN-TensorFlow](https://github.com/yifanw90/FSRCNN-TensorFlow)

**PyTorch** [https://github.com/yippp/FSRCNN](https://github.com/yippp/FSRCNN)

