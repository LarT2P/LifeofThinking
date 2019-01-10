![本处有图](https://pic4.zhimg.com/80/v2-757bffe781eedec526d0fd88d1004a0b_hd.jpg)

今天看到一个有意思的文章《证伪：CNN中的图片平移不变性》(https://zhuanlan.zhihu.com/p/38024868)，里面讲了一篇2018年的论文，论文提到了一个很有意思的观点：

> 希伯来大学的Aharon Azulay和Yair Weiss近期发表的《Why do deep convolutional networks generalize so poorly to small image transformations?》这篇文章发现当小尺寸图像发生平移后，CNN会出现识别错误的现象，而且这一现象是普遍的。
>
> 通常我们认为深度CNN对图像的平移、形变具有不变性，但本文却揭示了这样一个现实：**当图像在当前平面上平移几个像素后，现代CNN（如VGG16、ResNet50和InceptionResNetV2）的输出会发生巨大改变，而且图像越小，网络的识别性能越差；同时，网络的深度也会影响它的错误率。**
>
> 论文通过研究表明，产生这个现象的 **主因是现代CNN体系结构没有遵循经典采样定理，无法保证通用性，而常用图像数据集的统计偏差也会使CNN无法学会其中的平移不变性**。综上所述，CNN在物体识别上的泛化能力还比不上人类。

而下面这篇《实验 | CNN与平移不变性》(https://zhuanlan.zhihu.com/p/42747793)，这样解释了下这里“采样定理”如何理解：

> 先看我的实验结论：
>
> 1. CNN丢失平移不变性是下采样（Pooling 和 Stride = 2的Conv）引起的。
> 
> 2. Max_Pooling其实对于平移不变性有一定的保持作用。
> 
> 3. 虽然在图像识别中最后会将Feature Map展平接FC, 但是既然全卷积网络也出现了平移不变性很差的情况，说明FC层不是主导因素。
> 
> ...
> 
> 说到底还是一个 “奈奎斯特采样定理”：为了不失真地恢复模拟信号，采样频率应该不小于模拟信号频谱中最高频率的2倍，f s≥2f max。
> 
> 而往往卷积网络出来的特征有高频成分，但是各种 Stride > 1的降采样操作都是低频采样。

<script src="https://cdn.jsdelivr.net/npm/texme@0.6.0"></script>
