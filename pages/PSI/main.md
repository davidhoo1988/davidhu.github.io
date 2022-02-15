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

注意表中实测的结果是以MD5做哈希函数得到得结果。为了尽可能达到伪阳率公式预期结果，需要选用性能好的哈希函数，即哈希函数的输出尽可能服从均匀分布。Bloom filter的c++实现可以参考[这里](https://github.com/ArashPartow/bloom)


### Threshold PSI
本篇文章侧重介绍PSI的一个变种形式，称之为Threshold PSI。这里阈值(threshold)指代的是，两个集合的交集大小(set cardinality)大于(或者小于)某个阈值t时才进行标准PSI操作；否则不进行标准PSI操作(不泄露交集内容)。大于阈值进行PSI操作的形式我们称之为over-threshold PSI; 小于阈值进行PSI操作的形式称之为below-threshold PSI。我主要参考这篇[论文](https://eprint.iacr.org/2018/184)中的相关描述。不失一般性地，下面我们仅讨论below-threshold PSI。

#### Below-Threshold PSI
首先理清一些PSI语境下的定义。在两个计算方参与的PSI协议中，第一个计算方P1通常作为客户端(client)拥有集合C,第二个计算方P2通常作为服务器端(server)拥有集合S。P1的输出始终是<img src="https://latex.codecogs.com/svg.image?\bot" title="\bot" />，它在整个协议过程中都不知道交集大小是不是小于阈值t。对P2而言，如果它的输出不是<img src="https://latex.codecogs.com/svg.image?\bot" title="\bot" />，那么它知道交集大小是否满足阈值条件；如果它的输出是<img src="https://latex.codecogs.com/svg.image?\bot" title="\bot" />，那么它不清楚究竟是因为两个集合是空集还是没有满足阈值条件导致的。这里我们假设计算双方并不知道对方集合的内容，但是知道对方集合的大小(即P1知道(C,|S|),P2知道(S,|C|))。综上所述，below-threshold可以形式化地定义如下：

<p align="center">
<img src="https://latex.codecogs.com/svg.image?((C,|S|),&space;(S,&space;|C|))&space;\mapsto&space;\left&space;\{\begin{aligned}(C\cap&space;S,\bot)&space;&&space;\text{&space;if&space;}&space;|C\cap&space;S|\leq&space;t&space;\\(\bot,&space;\bot)&space;&&space;\text{&space;otherwise&space;}\end{aligned}\right&space;." title="((C,|S|), (S, |C|)) \mapsto \left \{\begin{aligned}(C\cap S,\bot) & \text{ if } |C\cap S|\leq t \\(\bot, \bot) & \text{ otherwise }\end{aligned}\right ." />
</p>

#### Encrypted PSI Cardinality (ePSI-CA)
为了构造Below-Threshold PSI,引入一个关键的元操作ePSI-CA，形式化地定义如下：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?((C,|S|,pk_1,sk_1),(S,|C|,pk_1))\mapsto&space;(\bot,&space;Enc(pk_1,|C\cap&space;S|))" title="((C,|S|,pk_1,sk_1),(S,|C|,pk_1))\mapsto (\bot, Enc(pk_1,|C\cap S|))" />
</p>
<div>
<span>这里P1生成密钥对</span><img src="https://latex.codecogs.com/svg.image?(pk_1,sk_1)," title="(pk_1,sk_1)"/> 
<span>P2获得P1的公钥</span><img src="https://latex.codecogs.com/svg.image?pk_1" title="pk_1"/>
<span>。ePSI-CA协议的计算结果是P1得到</span><img src="https://latex.codecogs.com/svg.image?\bot" title="\bot" style="float: right;"/>
<span>，P2得到加密状态下的交集大小</span><img src="https://latex.codecogs.com/svg.image?Enc(pk_1,|C\cap&space;S|)" title="Enc(pk_1,|C\cap S|)" />
</div>
关于ePSI-CA的构造细节，请参考这个页面。


