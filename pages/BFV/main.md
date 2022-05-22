# BFV方案
BGV属于第二代FHE。最早有Junfeng Fan and Frederik Vercauteren于2012年在[这篇文章](https://eprint.iacr.org/2012/144)首次提出。
BFV最重要的贡献是在标准RLWE加密技术之上直接构造了FHE。


## BFV基本构造
 不同于BGV自己构造了一种新的RLWE变种，BFV构造直接建立在标准RLWE加密基础之上。整个密钥生成，加解密过程可用下图表示：
 <p align="center">
  <img src="fig/BFV_overview.png" alt="animated"/>
</p>

对BFV构造的一些解读：
1. 关于密钥生成，sk是从离散高斯分布（均值为0，方差很小）随机采样得到；pk本质是就是RLWE(0),所以pk和均匀分布在计算上不可区分。
2. 加密得到的ct本质上是有一对RLWE密文组成,即 <img src="https://latex.codecogs.com/svg.image?\mathbf{ct}\gets&space;(RLWE_u(m),&space;RLWE_u(0))" title="https://latex.codecogs.com/svg.image?\mathbf{ct}\gets (RLWE_u(m), RLWE_u(0))" /> 。特别注意RLWE加密所用的密钥u是Alice随机生成的，Bob无法得知u。
3. 解密部分的核心是如何“消除”密钥u的影响，因为Bob不可能得到u，所以无法对ct直接解密。但是Bob知晓ct密文的一个结构特征：<img src="https://latex.codecogs.com/svg.image?pk[0]&plus;s\cdot&space;pk[1]=e\approx&space;0" title="https://latex.codecogs.com/svg.image?pk[0]+s\cdot pk[1]=e\approx 0" />，从而 <img src="https://latex.codecogs.com/svg.image?pk[0]\cdot&space;u&plus;s\cdot&space;pk[1]\cdot&space;u=e\cdot&space;u\approx&space;0" title="https://latex.codecogs.com/svg.image?pk[0]\cdot u+s\cdot pk[1]\cdot u=e\cdot u\approx 0" /> 。

## BFV同态运算
这里具体讨论给定计算深度的同态计算模式，即leveled/somewhat FHE。

### 同态加法
加法很容易做到。只需要对相应的密文向量进行向量加法即可（另一种理解方法，将密文向量理解成degree-2 polynomial的系数向量，因此做多项式加法），噪声增长是加性的。

### 同态乘法
重点讨论乘法。首先将BFV密文向量理解成degree-2 polynomial, 即 <img src="https://latex.codecogs.com/svg.image?\mathbf{ct_i}\cong&space;ct_i[0]&plus;ct_i[1]x" title="https://latex.codecogs.com/svg.image?\mathbf{ct_i}\cong ct_i[0]+ct_i[1]x" /> 。

那么定义两个BFV密文的同态乘法运算如下：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\mathbf{ct_0}\times&space;\mathbf{ct_1}=(\mathbf{ct_0}[0]\cdot&space;\mathbf{ct_1}[0],&space;\mathbf{ct_0}[0]\cdot&space;\mathbf{ct_1}[1]&plus;\mathbf{ct_0}[1]\cdot&space;\mathbf{ct_1}[0],\mathbf{ct_0}[1]\cdot&space;\mathbf{ct_1}[1])" title="https://latex.codecogs.com/svg.image?\mathbf{ct_0}\times \mathbf{ct_1}=(\mathbf{ct_0}[0]\cdot \mathbf{ct_1}[0], \mathbf{ct_0}[0]\cdot \mathbf{ct_1}[1]+\mathbf{ct_0}[1]\cdot \mathbf{ct_1}[0],\mathbf{ct_0}[1]\cdot \mathbf{ct_1}[1])" />
</p>

引理：
