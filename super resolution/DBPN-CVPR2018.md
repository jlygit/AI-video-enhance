# DBPN #

文中说，现有的深度前馈 feed-forward SR网络可以学习低分辨率输入的表示，以及从低分辨率输入到高分辨率输出的非线性映射关系，然而却不能完全解决LR与HR之间相互的依赖关系（没有反馈feedback connections连接--）。

所以使用Deep Back-Projection Networks 网络来探索up-和down-采样层，为每个阶段的投影误差projection errors提供误差反馈机制。上采样和下采样阶段是相互连接的，每个阶段代表不同类型的图像退化和高分辨率组件。好高深，继续虐一下。

DBPN使用了iterative errorcorrecting feedback mechanism，迭代误差校正机制，同时计算上采样、下采样的投影误差projection errors来指导重建高质量的HR，这里下面再解释。

上下采用之间相互连接（不像前馈SR结构都是只有input到output的连接映射，这种方式在大倍上采样中首先与LR空间的有限特征，所以不能很好求得LR到HR的映射关系），所以DBPN不仅用上采样层获得HR特征，同时也用下采样层获得LR的反馈特征。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221104125.jpg)

为什么要这样呢？作者总结了目前SR结构的分类，如上图：

- （a）为预先通过传统插值如bicubic来上采样得到预超分的图，再通过卷积学习到最后的HR预测结果，如SRCNN、VDSR等。但是传统插值上采样也许会引起噪音。
- （b）为LR图像经过不断特征学习与传播，最后通过反卷积或sub-pixel卷积来重建HR结果，如FSRCNN、ESPCN、EDSR等。这种方式受限于网络深度和容量，所以学习不到非常复杂的特征映射关系（比如EDSR也是这种方式，对于高倍上采样就需要非常多层卷积和每次非常多的特征图）。
- （c）为金字塔模式，如LapSRNA，通过单向推理得的逐级学习特征然后上采样x2的方式，多级后最后可以得到多倍的HR图像
- （d）本文提出的方式，首先输入LR经过卷积提取特征，然后使用Back-projection的迭代来最小化重建误差，分为up-和downprojection，不断迭代（每个阶段由一个up-和一个downprojection构成），这种结构通过将构建误差分布到每个阶段，得到不同深度的SR特征，然后不同深度的SR特征通过concat连接起来，最后经过卷积获得最好逇HR预测。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221104633.jpg)

如上图，up-projection单元，由阶段t-1到t的过程是，首先低分辨率图像L<sup>t-1</sup>经过反卷积上采样到高分辨率H0<sup>t</sup>，H0<sup>t</sup>经过卷积降采样到低分辨率L0<sup>t</sup>，低分辨率L0<sup>t</sup>与输入L<sup>t-1</sup>计算残差（error），然后残差经过反卷积得到高分辨率残差H1<sup>t</sup>，然后与高分辨率的H0<sup>t</sup>相加得到输出t阶段的上采样高分辨率图像H<sup>t</sup>


同理down-projection单元，首先高分辨率图像H经过卷积下采样到低分辨率L0<sup>t</sup>，然后反卷积到高分辨率H0<sup>t</sup>并与H<sup>t</sup>计算高分辨率残差，残差经过卷积下采样得到低分辨率残差L1<sup>t</sup>，然后与低分辨率L0<sup>t</sup>相加得到t阶段的下采样的低分辨率L<sup>t</sup>图像。

这两个单元使用比较大的卷积核，如8* 8或12* 12。大卷积核因为难以收敛和可能产生局部最优解的缺点很少在先前文献中被使用，但是这里的迭代单元可以抑制这种缺点。


![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221111337.jpg)

介绍完两个组件，再看文中还提出了Dense DBPN来改善提升DBPN效果（为了减缓梯度下降了，受启发于DenseNets）。其实也就是在原来网络加入dense，所有up-projection输出都连接到后面的down-projection作为输入(特征reuse)，同理down-projection的输出都连接到后面的up-projection作为输入。如上图所示为Dense projection units，前面阶段的输出concat作为此阶段的输入，其中不同原始DenseNets，D-DBPN避免使用dropout和batch norm，因为它们移除了特征范围，不适合SR任务，所以这里concat后使用1* 1的卷积来feature pooling and dimensional reduction。据说此法非常有效，最后看下图为D-DBPN的网络结构。

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221105931.jpg)

直接看效果，X8超分，效果顶呱呱的

![](https://github.com/jlygit/AI-video-enhance/blob/master/super%20resolution/image/QQ%E6%88%AA%E5%9B%BE20181221112557.jpg)

## 开源实现 ##

**论文链接** [https://www.toyota-ti.ac.jp/Lab/Denshi/iim/members/muhammad.haris/projects/DBPN.html](https://www.toyota-ti.ac.jp/Lab/Denshi/iim/members/muhammad.haris/projects/DBPN.html)

**tensorflow** [https://github.com/tlokeshkumar/DBPN-tf](https://github.com/tlokeshkumar/DBPN-tf)


**PyTorch** [https://github.com/alterzero/DBPN-Pytorch](https://github.com/alterzero/DBPN-Pytorch)

**caffe** [https://github.com/alterzero/DBPN-caffe](https://github.com/alterzero/DBPN-caffe)

**Keras** [https://github.com/rajatkb/DBPN-Keras](https://github.com/rajatkb/DBPN-Keras)
