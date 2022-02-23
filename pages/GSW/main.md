# GSW 介绍
GSW是公认的第一个第三代FHE方案。Craig Gentry, Amit Sahai 和 Brent Waters最先提出该方案，其成果发表在[CRYPTO13](https://eprint.iacr.org/2013/340)。GSW最主要的贡献是探索了一条新的构造同态乘法的路径。在第二代FHE方案中，同态乘法的噪声增加比同态加法要大的多。GSW提出新的构造可以更有效的控制同态乘法运算的噪声增长。作为代价，它的密文长度(矩阵形式)也要大的多。GSW方案是后续的FHEW方案和TFHE方案的基石。FHEW/TFHE正是借用GSW的同态乘法构造了新的bootstrapping算法。

## 预备知识
### RLWE 的基本结构和同态运算
首先，我们回顾RLWE(Learning With Errors over Rings)的代数构造。这里注意区别两个概念：加密(encrypt)和编码(encode)。 RLWE加密有如下形式：
<p align="center">
  <img src="https://latex.codecogs.com/svg.image?RLWE_s(\widetilde{m})=(a,as&plus;e&plus;\widetilde{m})" title="RLWE_s(\widetilde{m})=(a,as+e+\widetilde{m})" />
</p>
<div>这里, <img src="https://latex.codecogs.com/svg.image?\widetilde{m}\in&space;R_q=R/qR=\mathbb{Z}_q[X]/(X^n&plus;1)" title="\widetilde{m}\in R_q=R/qR=\mathbb{Z}_q[X]/(X^n+1)" /> 是编码之后的明文(假定明文 <img src="https://latex.codecogs.com/svg.image?m\in&space;R_t&space;(t\ll&space;q)" title="m\in R_t (t\ll q)" /> , 编码则可以表示为 <img src="https://latex.codecogs.com/svg.image?\widetilde{m}=(q/t)m" title="\widetilde{m}=(q/t)m" /> )；<img src="https://latex.codecogs.com/svg.image?s\in&space;R=\mathbb{Z}[X]/(X^n&plus;1)" title="s\in R=\mathbb{Z}[X]/(X^n+1)" /> 是密钥。具体地，随机均匀地选取 <img src="https://latex.codecogs.com/svg.image?a\gets&space;R_q" title="a\gets R_q" /> ; 随机从离散高斯分布(均值为0，方差为 <img src="https://latex.codecogs.com/svg.image?\sigma" title="\sigma" /> ) 选取 <img src="https://latex.codecogs.com/svg.image?e\gets&space;\chi_{\sigma}^n" title="e\gets \chi_{\sigma}^n" /> ; 随机从某个离散‘窄’分布选取密钥（比如 <img src="https://latex.codecogs.com/svg.image?s\gets&space;\chi_{\sigma}^n" title="s\gets \chi_{\sigma}^n" /> 或 <img src="https://latex.codecogs.com/svg.image?s\gets&space;\{0,1,-1\}^n" title="s\gets \{0,1,-1\}^n" /> ）</div>

相应地，给定一个RLWE密文 <img src="https://latex.codecogs.com/svg.image?(a,b)\in&space;R_q^2" title="(a,b)\in R_q^2" /> ，它的解密形式可以用下式表达
<p align="center">
<img src="https://latex.codecogs.com/svg.image?RLWE_s^{-1}(a,b)=b-as=\widetilde{m}&plus;e" title="RLWE_s^{-1}(a,b)=b-as=\widetilde{m}+e" />
</p>
<div>对进行解码(decode)恢复出最后的明文(前提条件是 <img src="https://latex.codecogs.com/svg.image?|e|<q/2t" title="|e|<q/2t" /> )：</div>
<p align="center">
<img src="https://latex.codecogs.com/svg.image?m=\lfloor\frac{t}{q}(\widetilde{m}&plus;e)\rceil" title="m=\lfloor\frac{t}{q}(\widetilde{m}+e)\rceil" />
</p>

接着，我们回顾RLWE密文的同态加法操作。

<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE_s(m_0)&plus;LWE_s(m_1)=(a_0,b_0)&plus;(a_1,b_1)=(a_0&plus;a_1,b_0&plus;b_1)=LWE_s(m_0&plus;m_1)" title="LWE_s(m_0)+LWE_s(m_1)=(a_0,b_0)+(a_1,b_1)=(a_0+a_1,b_0+b_1)=LWE_s(m_0+m_1)" />
</p>
类似地，可得和同态标量乘法操作。

<p align="center">
<img src="https://latex.codecogs.com/svg.image?d\cdot&space;LWE_s(m_0)=d\cdot&space;(a_0,b_0)=(d\cdot&space;a_0,d\cdot&space;b_0)=LWE_s(d\cdot&space;m_0)" title="d\cdot LWE_s(m_0)=d\cdot (a_0,b_0)=(d\cdot a_0,d\cdot b_0)=LWE_s(d\cdot m_0)" />
</p>

分析一下加法操作和标量乘操作的噪声增长。

最后，我们试着构造RLWE密文的同态乘法操作。

### GSW 密码方案
#### 直觉

#### 正式的构造
