# BFV方案
BGV属于第二代FHE。最早有Junfeng Fan and Frederik Vercauteren于2012年在[这篇文章](https://eprint.iacr.org/2012/144)首次提出。
BFV最重要的贡献是在标准RLWE加密技术之上直接构造了FHE。

## 标准RLWE加密
在正式介绍BFV构造之前，回忆一遍标准RLWE加密的细节。


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
重点讨论乘法。首先将RLWE密文向量理解成degree-1 polynomial, 即 <img src="https://latex.codecogs.com/svg.image?\mathbf{ct_i}\cong&space;ct_i[0]&plus;ct_i[1]x" title="https://latex.codecogs.com/svg.image?\mathbf{ct_i}\cong ct_i[0]+ct_i[1]x" /> 。

那么定义两个BFV密文的同态乘法运算(初始版本)如下：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\mathbf{ct_{\times}}\overset{\underset{\mathrm{def}}{}}{=}\mathbf{ct_0}\times&space;\mathbf{ct_1}=(\mathbf{ct_0}[0]\cdot&space;\mathbf{ct_1}[0],&space;\mathbf{ct_0}[0]\cdot&space;\mathbf{ct_1}[1]&plus;\mathbf{ct_0}[1]\cdot&space;\mathbf{ct_1}[0],\mathbf{ct_0}[1]\cdot&space;\mathbf{ct_1}[1])" title="https://latex.codecogs.com/svg.image?\mathbf{ct_{\times}}\overset{\underset{\mathrm{def}}{}}{=}\mathbf{ct_0}\times \mathbf{ct_1}=(\mathbf{ct_0}[0]\cdot \mathbf{ct_1}[0], \mathbf{ct_0}[0]\cdot \mathbf{ct_1}[1]+\mathbf{ct_0}[1]\cdot \mathbf{ct_1}[0],\mathbf{ct_0}[1]\cdot \mathbf{ct_1}[1])" />
</p>
这个乘法定义借助多项式乘法就很好理解: 
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\mathbf{ct_0}\times&space;\mathbf{ct_1}=(\mathbf{ct_0}[0]&plus;\mathbf{ct_0}[1]x)\cdot&space;(\mathbf{ct_1}[0]&plus;\mathbf{ct_1}[1]x)&space;=&space;\mathbf{ct_0}[0]\cdot&space;\mathbf{ct_1}[0]&plus;(\mathbf{ct_0}[0]\cdot&space;\mathbf{ct_1}[1]&plus;\mathbf{ct_0}[1]\cdot&space;\mathbf{ct_1}[0])x&plus;\mathbf{ct_0}[1]\cdot&space;\mathbf{ct_1}[1]x^2" title="https://latex.codecogs.com/svg.image?\mathbf{ct_0}\times \mathbf{ct_1}=(\mathbf{ct_0}[0]+\mathbf{ct_0}[1]x)\cdot (\mathbf{ct_1}[0]+\mathbf{ct_1}[1]x) = \mathbf{ct_0}[0]\cdot \mathbf{ct_1}[0]+(\mathbf{ct_0}[0]\cdot \mathbf{ct_1}[1]+\mathbf{ct_0}[1]\cdot \mathbf{ct_1}[0])x+\mathbf{ct_0}[1]\cdot \mathbf{ct_1}[1]x^2" />
</p>
也就是说，BFV同态乘法的结果就是取相应密文多项式乘法结果的系数。

现在，有两个新问题要解决
1. BFV同态乘法得到的结果进行解密得到的是<img src="https://latex.codecogs.com/svg.image?\Delta^2m_0m_1&plus;noise" title="https://latex.codecogs.com/svg.image?\Delta^2m_0m_1+noise" />，而我们想要的是 <img src="https://latex.codecogs.com/svg.image?\Delta&space;m_0m_1&plus;noise" title="https://latex.codecogs.com/svg.image?\Delta m_0m_1+noise" /> 。
2. 和BGV乘法类似的情况，需要做key-switch将三维的密文向量降到二维。

先讨论第一个问题。具体地，对BFV乘法结果解密得到 
<p align="center">
<img src="https://latex.codecogs.com/svg.image?(\mathbf{ct_0}[0]&plus;\mathbf{ct_0}[1]s)\cdot&space;(\mathbf{ct_1}[0]&plus;\mathbf{ct_1}[1]s)=(\Delta&space;m_0&plus;noise_0)\cdot&space;(\Delta&space;m_1&plus;noise_1)=\Delta^2m_0m_1&plus;\Delta(m_0noise_1&plus;m_1noise_0)&plus;noise_0\cdot&space;noise_1" title="https://latex.codecogs.com/svg.image?(\mathbf{ct_0}[0]+\mathbf{ct_0}[1]s)\cdot (\mathbf{ct_1}[0]+\mathbf{ct_1}[1]s)=(\Delta m_0+noise_0)\cdot (\Delta m_1+noise_1)=\Delta^2m_0m_1+\Delta(m_0noise_1+m_1noise_0)+noise_0\cdot noise_1" />
</p>
<div>那么对密文向量做‘rounding/rescale’(类似于Mod-Switch)就可以化简得到 <img src="https://latex.codecogs.com/svg.image?\Delta&space;m_0m_1&plus;noise'" title="https://latex.codecogs.com/svg.image?\Delta m_0m_1+noise'" /> 。具体地，定义rounding/rescale 操作如下:</div>

<p align="center">
<img src="https://latex.codecogs.com/svg.image?Rescale(\mathbf{ct_{\times}})\overset{\underset{\mathrm{def}}{}}{=}(\lfloor&space;\frac{t}{q}\cdot\mathbf{ct_{\times}}[0]\rceil,&space;\lfloor&space;\frac{t}{q}\cdot\mathbf{ct_{\times}}[1]\rceil,&space;\lfloor&space;\frac{t}{q}\cdot\mathbf{ct_{\times}}[2]\rceil)" title="https://latex.codecogs.com/svg.image?Rescale(\mathbf{ct_{\times}})\overset{\underset{\mathrm{def}}{}}{=}(\lfloor \frac{t}{q}\cdot\mathbf{ct_{\times}}[0]\rceil, \lfloor \frac{t}{q}\cdot\mathbf{ct_{\times}}[1]\rceil, \lfloor \frac{t}{q}\cdot\mathbf{ct_{\times}}[2]\rceil)" />
</p>

引理：<img src="https://latex.codecogs.com/svg.image?RLWE.Decrypt(rescale(\mathbf{ct_{\times}}))=\Delta\cdot&space;m_0m_1&plus;noise" title="https://latex.codecogs.com/svg.image?RLWE.Decrypt(rescale(\mathbf{ct_{\times}}))=\Delta\cdot m_0m_1+noise" />

证明：首先我们有 <img src="https://latex.codecogs.com/svg.image?\sum_{i=0}^{2}\lfloor\frac{t}{q}\cdot\mathbf{ct_{\times}}[i]x^i\rceil=\frac{t}{q}\sum_i&space;\mathbf{ct_{\times}}[i]x^i&plus;\sum_ir_ix^i\approx&space;\frac{t}{q}\sum_i&space;\mathbf{ct_{\times}}[i]x^i" title="https://latex.codecogs.com/svg.image?\sum_{i=0}^{2}\lfloor\frac{t}{q}\cdot\mathbf{ct_{\times}}[i]x^i\rceil=\frac{t}{q}\sum_i \mathbf{ct_{\times}}[i]x^i+\sum_ir_ix^i\approx \frac{t}{q}\sum_i \mathbf{ct_{\times}}[i]x^i" />, 这里 <img src="https://latex.codecogs.com/svg.image?r_i\sim&space;Unif(-1/2,1/2)" title="https://latex.codecogs.com/svg.image?r_i\sim Unif(-1/2,1/2)" /> 。令 <img src="https://latex.codecogs.com/svg.image?s\sim&space;\mathcal{N}(0,\sigma_s^2)" title="https://latex.codecogs.com/svg.image?s\sim \mathcal{N}(0,\sigma_s^2)" />， 特别注意有 <img src="https://latex.codecogs.com/svg.image?\sum_i&space;r_is^i\sim&space;\mathcal{N}(0,&space;\frac{1}{12}&plus;&space;n\cdot&space;\sigma_s^2\cdot&space;\frac{1}{12}&plus;n\cdot&space;\sigma_s^4\cdot&space;\frac{1}{12})" title="https://latex.codecogs.com/svg.image?\sum_i r_is^i\sim \mathcal{N}(0, \frac{1}{12}+ n\cdot \sigma_s^2\cdot \frac{1}{12}+n\cdot \sigma_s^4\cdot \frac{1}{12})" /> 。
接着考虑 <img src="https://latex.codecogs.com/svg.image?\frac{t}{q}\sum_i\mathbf{ct_i}[i]x^i=(1-\frac{t}{q}r)\Delta&space;m_0m_1&plus;(1-\frac{t}{q}r)(m_0e_1&plus;m_1e_0)&plus;\frac{t}{q}e_0e_1=\Delta&space;m_0m_1&plus;noise" title="https://latex.codecogs.com/svg.image?\frac{t}{q}\sum_i\mathbf{ct_i}[i]x^i=(1-\frac{t}{q}r)\Delta m_0m_1+(1-\frac{t}{q}r)(m_0e_1+m_1e_0)+\frac{t}{q}e_0e_1=\Delta m_0m_1+noise" />, 更进一步分析噪声分量有 <img src="https://latex.codecogs.com/svg.image?noise\approx&space;m_0e_1&plus;m_1e_0&plus;rm_0m_1\sim&space;(0,&space;2n\cdot&space;t^2\sigma^2)" title="https://latex.codecogs.com/svg.image?noise\approx m_0e_1+m_1e_0+rm_0m_1\sim (0, 2n\cdot t^2\sigma^2)" /> 
综上所述，总噪声的分布方差上限一定是一个小数目，具体数值为 <img src="https://latex.codecogs.com/svg.image?n\cdot&space;\sigma_s^2\cdot&space;\frac{1}{12}&plus;n\cdot&space;\sigma_s^4\cdot&space;\frac{1}{12}&plus;2n\cdot&space;t^2\sigma^2" title="https://latex.codecogs.com/svg.image?n\cdot \sigma_s^2\cdot \frac{1}{12}+n\cdot \sigma_s^4\cdot \frac{1}{12}+2n\cdot t^2\sigma^2" /> 。

现在讨论第二个问题 re-linearization, 即给定三维的密文向量，如何同态地约减到二维：
 <p align="center">
  <img src="fig/relinearize.png" alt="animated"/>
</p>

BFV设计了两种形式的re-linearization。第一种形式和BGV-FHE介绍的一样：
 <p align="center">
  <img src="fig/relinearize_v1.png" alt="animated"/>
</p>

下面证明re-linearization第一种形式的正确性。首先注意到 
 <p align="center">
<img src="https://latex.codecogs.com/svg.image?c_2'[i]\cdot&space;(rlk_i[0],&space;rlk_i[1])=RLWE_s(c_2'[i]\cdot&space;s^2T^i)&space;~with~&space;Err(RLWE_s(c_2'[i]\cdot&space;s^2T^i))\sim&space;\mathcal{N}(0,\frac{T^2}{12}\cdot&space;\sigma_{rlk}^2)" title="https://latex.codecogs.com/svg.image?c_2'[i]\cdot (rlk_i[0], rlk_i[1])=RLWE_s(c_2'[i]\cdot s^2T^i) ~with~ Err(RLWE_s(c_2'[i]\cdot s^2T^i))\sim \mathcal{N}(0,\frac{T^2}{12}\cdot \sigma_{rlk}^2)" />，</p>
 
 因此 
 <p align="center">
 <img src="https://latex.codecogs.com/svg.image?\sum_ic_2'[i]\cdot&space;(rlk_i[0],&space;rlk_i[1])=RLWE_s(\sum_ic_2'[i]\cdot&space;s^2T^i)=RLWE_s(c_2'\cdot&space;s^2)&space;~with~&space;Err(RLWE_s(c_2'\cdot&space;s^2))\sim&space;\mathcal{N}(0,logq\cdot\frac{T^2}{12}\cdot&space;\sigma_{rlk}^2)" title="https://latex.codecogs.com/svg.image?\sum_ic_2'[i]\cdot (rlk_i[0], rlk_i[1])=RLWE_s(\sum_ic_2'[i]\cdot s^2T^i)=RLWE_s(c_2'\cdot s^2) ~with~ Err(RLWE_s(c_2'\cdot s^2))\sim \mathcal{N}(0,logq\cdot\frac{T^2}{12}\cdot \sigma_{rlk}^2)" />
</p>

第二种形式的re-linearization借助了rescale思想：
 <p align="center">
  <img src="fig/relinearize_v2.png" alt="animated"/>
</p>

下面证明re-linearization第二种形式的正确性。 这里需要利用关系式 <img src="https://latex.codecogs.com/svg.image?\lfloor&space;\frac{a}{p}\rceil=\frac{a}{p}&plus;r&space;~with~&space;r\sim&space;Unif(-\frac{1}{2},\frac{1}{2})" title="https://latex.codecogs.com/svg.image?\lfloor \frac{a}{p}\rceil=\frac{a}{p}+r ~with~ r\sim Unif(-\frac{1}{2},\frac{1}{2})" /> 。首先容易证明 <img src="https://latex.codecogs.com/svg.image?c_2'\cdot&space;(rlk[0],&space;rlk[1])=RLWE_s^{pq}(pc_2'\cdot&space;s)~with~&space;Err\sim&space;\mathcal{N}(0,c_2'^2\cdot&space;\sigma_{rlk}^2)" title="https://latex.codecogs.com/svg.image?c_2'\cdot (rlk[0], rlk[1])=RLWE_s^{pq}(pc_2'\cdot s)~with~ Err\sim \mathcal{N}(0,c_2'^2\cdot \sigma_{rlk}^2)" /> ；接着做rescale得到 <img src="https://latex.codecogs.com/svg.image?(\lfloor\frac{c_2'\cdot&space;rlk[0]}{p}\rceil&space;,&space;\lfloor\frac{c_2'\cdot&space;rlk[1]}{p}\rceil)&space;=RLWE_s^{p}(c_2'\cdot&space;s)~with~&space;Err\sim&space;\mathcal{N}(0,\frac{c_2'^2\cdot&space;\sigma_{rlk}^2}{p^2}&plus;\frac{n\sigma_s^2}{12}&plus;\frac{1}{12})" title="https://latex.codecogs.com/svg.image?(\lfloor\frac{c_2'\cdot rlk[0]}{p}\rceil , \lfloor\frac{c_2'\cdot rlk[1]}{p}\rceil) =RLWE_s^{p}(c_2'\cdot s)~with~ Err\sim \mathcal{N}(0,\frac{c_2'^2\cdot \sigma_{rlk}^2}{p^2}+\frac{n\sigma_s^2}{12}+\frac{1}{12})" /> 。

综上所述，完全形态的BFV同态乘法算法(输入 <img src="https://latex.codecogs.com/svg.image?\mathbf{ct_0}=RLWE(m_0),\mathbf{ct_1}=RLWE(m_1)" title="https://latex.codecogs.com/svg.image?\mathbf{ct_0}=RLWE(m_0),\mathbf{ct_1}=RLWE(m_1)" /> )如下：
1. 计算 <img src="https://latex.codecogs.com/svg.image?\mathbf{ct_{\times}}\overset{\underset{\mathrm{def}}{}}{=}\mathbf{ct_0}\times&space;\mathbf{ct_1}=(\mathbf{ct_0}[0]\cdot&space;\mathbf{ct_1}[0],&space;\mathbf{ct_0}[0]\cdot&space;\mathbf{ct_1}[1]&plus;\mathbf{ct_0}[1]\cdot&space;\mathbf{ct_1}[0],\mathbf{ct_0}[1]\cdot&space;\mathbf{ct_1}[1])" title="https://latex.codecogs.com/svg.image?\mathbf{ct_{\times}}\overset{\underset{\mathrm{def}}{}}{=}\mathbf{ct_0}\times \mathbf{ct_1}=(\mathbf{ct_0}[0]\cdot \mathbf{ct_1}[0], \mathbf{ct_0}[0]\cdot \mathbf{ct_1}[1]+\mathbf{ct_0}[1]\cdot \mathbf{ct_1}[0],\mathbf{ct_0}[1]\cdot \mathbf{ct_1}[1])" /> 
2. 做Rescale操作得 <img src="https://latex.codecogs.com/svg.image?Rescale(\mathbf{ct_{\times}})\overset{\underset{\mathrm{def}}{}}{=}(\lfloor&space;\frac{t}{q}\cdot\mathbf{ct_{\times}}[0]\rceil,&space;\lfloor&space;\frac{t}{q}\cdot\mathbf{ct_{\times}}[1]\rceil,&space;\lfloor&space;\frac{t}{q}\cdot\mathbf{ct_{\times}}[2]\rceil)" title="https://latex.codecogs.com/svg.image?Rescale(\mathbf{ct_{\times}})\overset{\underset{\mathrm{def}}{}}{=}(\lfloor \frac{t}{q}\cdot\mathbf{ct_{\times}}[0]\rceil, \lfloor \frac{t}{q}\cdot\mathbf{ct_{\times}}[1]\rceil, \lfloor \frac{t}{q}\cdot\mathbf{ct_{\times}}[2]\rceil)" />
3. 做Relinearization操作得 <img src="https://latex.codecogs.com/svg.image?\mathbf{ct_\times}\gets&space;Relinearize(Rescale(\mathbf{ct_{\times}}),&space;\{\mathbf{rlk_i}\})" title="https://latex.codecogs.com/svg.image?\mathbf{ct_\times}\gets Relinearize(Rescale(\mathbf{ct_{\times}}), \{\mathbf{rlk_i}\})" /> 。


## Single Instruction Multiple Data 编码方式 (SIMD)
第二代FHE最强大的特性就是SIMD。以BFV方案为例，这意味着一个BFV密文可以对一个向量进行加密 (相当于加密多个数据构成的数组，而不是仅仅对一个数据进行加密）。 之所以能支持这个特性，是因为Number Theoretic Transform (NTT)的缘故。注意到BFV加密的明文是一个多项式 <img src="https://latex.codecogs.com/svg.image?m(x)\in&space;\mathbb{Z}_q/(X^n&plus;1)" title="https://latex.codecogs.com/svg.image?m(x)\in \mathbb{Z}_q/(X^n+1)" />, NTT的本质是对m(X)在特定n个点上的估值，具体定义如下：

<p align="center">
<img src="https://latex.codecogs.com/svg.image?NTT_n(m(x))\overset{\underset{\mathrm{def}}{}}{=}\{m(\zeta^i)\}_{i=1,3,\cdots,2n-1}=(m(\zeta),m(\zeta^3),\cdots,m(\zeta^{2n-1}))&space;~where~&space;\zeta&space;\text{&space;is&space;a&space;2n-th&space;primitive&space;root&space;of&space;unity.}" title="https://latex.codecogs.com/svg.image?NTT_n(m(x))\overset{\underset{\mathrm{def}}{}}{=}\{m(\zeta^i)\}_{i=1,3,\cdots,2n-1}=(m(\zeta),m(\zeta^3),\cdots,m(\zeta^{2n-1})) ~where~ \zeta \text{ is a 2n-th primitive root of unity.}" />
</p>

NTT是一个线性运算，容易得到NTT的逆运算使得 <img src="https://latex.codecogs.com/svg.image?NTT^{-1}(\{m(\zeta^i)\}_{i=1,3,\cdots,2n-1})=m(x)\in&space;\mathbb{Z}_q[X]/(X^n&plus;1)" title="https://latex.codecogs.com/svg.image?NTT^{-1}(\{m(\zeta^i)\}_{i=1,3,\cdots,2n-1})=m(x)\in \mathbb{Z}_q[X]/(X^n+1)" />。 因此利用NTT逆运算可以将一组向量编码成一个多项式，在FHE语境中，又称之为SIMD encoding 技术，即
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\{m(\zeta^i)\}_{i=1,3,\cdots,2n-1}\xrightarrow[]{Encode/NNT^{-1}}&space;m(X)\in&space;R_q" title="https://latex.codecogs.com/svg.image?\{m(\zeta^i)\}_{i=1,3,\cdots,2n-1}\xrightarrow[]{Encode/NNT^{-1}} m(X)\in R_q" />
</p>

## 比较BFV和BGV

