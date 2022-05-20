# BFV方案
BGV属于第二代FHE。最早有Junfeng Fan and Frederik Vercauteren于2012年在[这篇文章](https://eprint.iacr.org/2012/144)首次提出。
BFV最重要的贡献是在标准RLWE加密技术之上直接构造了FHE。


 不同于BGV自己构造了一种新的RLWE变种，BFV构造直接建立在标准RLWE加密基础之上。整个密钥生成，加解密过程可用下图表示：
 <p align="center">
  <img src="fig/BFV_overview.png" alt="animated"/>
</p>

## BFV基本构造

对BFV构造的一些解读：
1. 关于密钥生成，sk是从离散高斯分布（均值为0，方差很小）随机采样得到；pk本质是就是RLWE(0),所以pk和均匀分布在计算上不可区分。
2. 加密得到的ct本质上是有一对RLWE密文组成,即 <img src="https://latex.codecogs.com/svg.image?\mathbf{ct}\gets&space;(RLWE_u(m),&space;RLWE_u(0))" title="https://latex.codecogs.com/svg.image?\mathbf{ct}\gets (RLWE_u(m), RLWE_u(0))" /> 。特别注意RLWE加密所用的密钥u是Alice随机生成的，Bob无法得知u。
3. 解密部分的核心是如何“消除”密钥u的影响，因为Bob不可能得到u，所以无法对ct直接解密。但是Bob知晓ct密文的一个结构特征：<img src="https://latex.codecogs.com/svg.image?pk[0]&plus;s\cdot&space;pk[1]=e\approx&space;0" title="https://latex.codecogs.com/svg.image?pk[0]+s\cdot pk[1]=e\approx 0" />

## BFV同态运算

### 同态加法

### 同态乘法
