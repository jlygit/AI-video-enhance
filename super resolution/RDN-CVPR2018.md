# RDN #

RDN-residual dense network,残差和稠密网络的结合。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221192238.jpg)

如上图，比较了EDSR和SRDenseNet的block结构，文中提出了Residual dense block（RDB），是由dense和residual组成的，dense可以充分利用前面层的特征，residual可以使得学习更快更容易收敛。RDB是组成RDN网络的核心组件。如下图，RDN为C个卷积组成，每个卷积的输出都作为后面卷积的输入（dense），最后经过1* 1卷积做特征融合，并与输入相加得到此RDB层的特征输出。RDB做了局部残差特征的学习和融合。

![https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221193543.jpg](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221193543.jpg)

RDN，如下图所示，由前到后共4个部分，首先是LR经过卷积提取特征F<sub>-1</sub>，然后再次经过卷积得到F<sub>0</sub>，称为特征提取层，接着是不同层次的高层特征提取，通过D个RDB组成的中间网络，这可以学习到不同层次的LR特征，然后下一部分是特征融合，使用concat连接所有RDB的特征，经过1* 1卷积做特征融合和降维，然后经过一个卷积后再加上低层特征F<sub>-1</sub>得到F<sub>DF</sub>，最后一部分就是上采样到HR目标大小的特征，然后使用最后一个卷积层来重建HR图像。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221192701.jpg)

RDN卷积层均使用3* 3的核（除了全局和局部特征融合之后的卷积核为1* 1），然后特征提取、局部、全局特征融合后的卷积最后是输出64个特征图。RDN里面的卷积生成的特征图个数为G。最后RDN输出的是3通道的彩色图像。

总体看，结构不是很难理解，不过很大很复杂，首先是D个RDB，每个RDB里面有C个卷积，每个卷积生成G个特征图，并且局部和全局通过dense和residual来连接。D、C、G的组合可以构造非常大和复杂的网络结果，文中的结果使用了D=16，C=8和G=64。

show time

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221193917.jpg)

## 开源实现 ##

**论文链接** [https://arxiv.org/pdf/1802.08797.pdf](https://arxiv.org/pdf/1802.08797.pdf)

**tensorflow**  [https://github.com/hengchuan/RDN-TensorFlow](https://github.com/hengchuan/RDN-TensorFlow)   [https://github.com/seathiefwang/RDN-Tensorflow](https://github.com/seathiefwang/RDN-Tensorflow)


**PyTorch** [https://github.com/lingtengqiu/RDN-pytorch](https://github.com/lingtengqiu/RDN-pytorch)

**Keras**  [https://github.com/rajatkb/RDNSR-Residual-Dense-Network-for-Super-Resolution-Keras](https://github.com/rajatkb/RDNSR-Residual-Dense-Network-for-Super-Resolution-Keras)

**Torch** [https://github.com/yulunzhang/RDN](https://github.com/yulunzhang/RDN)
