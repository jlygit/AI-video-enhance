# RCAN #

非常深的residual channel attention networks（RCAN），文中两个亮点：

- residual in residual (RIR) structure基于残差的残差结构
- channel attention mechanism通道注意力机制

作者认为目前大部分算法 treat channel-wise features equally，就是对每个通道特征都是同样的重要程度，这样缺乏灵活处理不同类型的信息，如高频、低频信息。往往高频细节信息更难学习，图像HR可以看做是恢复尽可能多高频信息的一个过程，因为LR包括的低频信息可以直接传给HR。所以RCAN提出了channel attention mechanism作用于不同通道的特征图，提高不同通道的特征图之间的差异性。而RIR的结构是解决非常深的网络提出来的一种结构。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181224110415.jpg)

如图，RCAN包括浅层特征提取（用一层卷积）、RIR深层特征提取、上采样和重建三个部分。

RIR结构为核心部分，包括了G个 residual groups (RG) 和一个 long skip connection (LSC，看做全局残差)，每个RG又包括B个 residual channel attention blocks (RCAB）和一个 short skip connection (SSC，看做局部残差)。RIR结构这种残差堆起来的方式允许网络深度达到400层以上，这就可以获得超好的性能了。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181224114316.jpg)

RCAB如上图所示，首先经过一个卷积，一个relu激活，然后一个卷积，最后接着Channel Attention（CA）结构，最后与输入相加。CA负责提取所有特征通道之间的统计信息来增强每个特征通道，提高通道之间的区分能力（Channel attention）。就是没有类似现有算法那样，将每个通道都同等对待。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181224115027.jpg)

如图，channel attention mechanism通道注意力的机制（本文主要的亮点之处），首先使用全局池化H<sub>GP</sub>统计z，也就是每通道H* W的特征图变成了1* 1的一个统计值。然后接着一个卷积W<sub>D</sub>将特征通道数降维channel-downscaling为C/r，然后使用relu激活范数，接着一个卷积W<sub>U</sub>将通道扩维 channel-upscaling为C，接着使用sigmoid函数作用后得到最后的s<sub>c</sub>，最后作用于输入（与输入矩阵进行点乘）。整个CA过程就是通过某些机制来给每个通道的feature计算一个channel attention值s<sub>c</sub>，最后作用于对应的通道特征图，起到通道之间的特征程度改变（重要度改变了）。

**实现细节**


文中使用G10个RG结构，每个RG结构使用20个RCAB。除了channel-downscaling与 channel-upscaling（卷积核1* 1，特征数C/r=4），所有卷积核使用3* 3，特征数为C=64。最后输出三通道彩色图。训练集使用2K图DIV2K。

如图，可以看到细节学习能力非常强。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181224144650.jpg)

## 开源实现 ##

**论文链接**  [https://arxiv.org/pdf/1807.02758.pdf](https://arxiv.org/pdf/1807.02758.pdf)

**tensorflow**  [https://github.com/kozistr/rcan-tensorflow](https://github.com/kozistr/rcan-tensorflow)

**PyTorch** [https://github.com/yulunzhang/RCAN](https://github.com/yulunzhang/RCAN)

