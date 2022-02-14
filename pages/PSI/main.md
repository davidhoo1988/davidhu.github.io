## PSI介绍

PSI(Private Set Intersection)是一种特殊的多方安全计算技术，用来计算两个集合的交集部分而不泄露这两个集合本身的内容。一般地，设有计算的双方，A和B，各自拥有集合SetA和SetB，并且，A不知道SetB的内容，B不知道SetA的内容。经过PSI运算后，A(或者B)最终得到SetA和SetB的交集Intersect，但是A仍然不知道SetB-Intersect部分。


### 预备知识
#### Bloom Filter
Bloom Filter是一种数据结构，用来记录某个对象(object)是否已经被存储而不需要存储这个对象本身。如果这个对象的存储代价很大，Bloom Filter就很有优势：因为这里我们只关心目标对象是不是已经被记录,而不关心它是不是完整地被存储(方便将来提取)。和其他数据结构相比，Bloom Filter往往在空间复杂度和时间复杂度上都更优。Bloom Filter的结构可以简单理解为一个数组，数组的每一个元素长度为1比特,用来记录某个特定的对象是否被记录的状态：‘1’表示已被记录， ‘0’表示没有被记录。

*算法：* Bloom Filter主要有两个算法，一个是添加操作，一个是检查操作。 添加操作向Bloom Filter添加一个对象存在的记录; 检查操作检查某一个特定的对象是否已被Bloom Filter记录。

添加操作如下：
1. 计算输入对象的哈希值(Hash value)
2. 视哈希值为地址，将该地址对应的Bloom Filter的状态置为1

检查操作如下:
1. 计算目标对象的哈希值
2. 视哈希值为地址，查看该地址对应的Bloom Filter的状态是否为1。若为1，那么目标对象已被记录；否则，没有被记录。

*性能：* 衡量Bloom Filter最重要的指标是伪阳率(False Positive Rate): 目标对象被Bloom Filter的检查操作认为已经记录，但实际上该对象并没有被记录。令k为哈希函数的个数（也就是Bloom Filter检查操作里需要检查的状态位的个数），n表示Bloom filter记录的对象个数，m表示Bloom filter的状态位个数(也就是Bloom filter数组的总长度)，那么伪阳率公式为:
<p align="center">
<img src="https://latex.codecogs.com/svg.image?(1-e^{-kn/m})^k" title="(1-e^{-kn/m})^k" />
</p>

简单地说，在Bloom Filter容量一定的条件下，增加哈希函数个数k可以显著的降低伪阳率，下表显示了当m=8KB=65536bits，n=500时，参数k对伪阳率的影响规律：
<table class="wp-block-table"><tbody><tr><td><em>k</em></td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td></tr><tr><td>预期伪阳率</td><td>0.00760</td><td>0.000229</td><td>1.16e-5</td><td>8.16e-7</td><td>7.35e-8</td><td>8.02e-9</td></tr><tr><td>实测伪阳率</td><td>0.00822</td><td>0.000155</td><td>2.22e-5</td><td>0</td><td>0</td><td>0</td></tr></tbody></table>

注意表中实测的结果是以MD5做哈希函数得到得结果。为了尽可能达到伪阳率公式预期结果，需要选用性能好的哈希函数，即哈希函数的输出尽可能服从均匀分布。
