# SRDenseNet #

 dense skip connections,本算法结合densenet和skip connections一起设计了基于CNN的非常深的超分结构。densenet是用于深层结构中缓解梯度消失问题的手段。densenet在每个block中间每一层的特征图都作为后面所有层的输入（如下图红色框里面的dense block），使得前面所有层学习到的特征都concat起来（不是resnet那种加起来），使得特征图个数变大了，这也是前面学习特征图的一种利用，加强了特征传播，减少了学习的参数量，当然，还缓解了梯度消失问题（不过确定是比较耗时和耗内存）。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181219201012.jpg)

文中提出三种结构，如上图，

- （a）输入LR图像经过卷积提取低层特征（low-level features），然后经过8个dense block提取高层特征（high level features），最后只对高层特征通过两个反卷积层（3* 3的核，256特征图）进行上采样和一个重建层得到HR的输出。
- （b）在最后将低层特征和高层特征concat起来，同时利用来做反卷积，最后通过重建层3* 3卷积操作获得单通道的HR图像。
- （c）提取高层特征的block，后面都利用上低层特征，那么所有block处理后，最后特征图数量一定很大，所以文中使用一个1* 1的卷积层bottleneck 来降低特征通道数。最后降低后的特征图256个再送入反卷积进行上采样（反卷积就是学习到一个上采样的核，可以看做卷积的逆）。

8个dense block一共有64个卷积层，最后block输出128个特征图。所以学习能力还是非常劲爆的，看图：

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181219202943.jpg)

## 开源实现 ##

**论文链接** [http://openaccess.thecvf.com/content_ICCV_2017/papers/Tong_Image_Super-Resolution_Using_ICCV_2017_paper.pdf](http://openaccess.thecvf.com/content_ICCV_2017/papers/Tong_Image_Super-Resolution_Using_ICCV_2017_paper.pdf)

**tensorflow** [https://github.com/ppooiiuuyh/SR_SRDenseNet_tensorflow](https://github.com/ppooiiuuyh/SR_SRDenseNet_tensorflow)

**pytorch**  [https://github.com/wxywhu/SRDenseNet-pytorch](https://github.com/wxywhu/SRDenseNet-pytorch)

**caffe** [https://github.com/xueshengke/SRDenseNet-Caffe](https://github.com/xueshengke/SRDenseNet-Caffe)

