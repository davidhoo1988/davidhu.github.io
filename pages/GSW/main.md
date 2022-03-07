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
类似地，可得同态标量乘法操作。

<p align="center">
<img src="https://latex.codecogs.com/svg.image?d\cdot&space;LWE_s(m_0)=d\cdot&space;(a_0,b_0)=(d\cdot&space;a_0,d\cdot&space;b_0)=LWE_s(d\cdot&space;m_0)" title="d\cdot LWE_s(m_0)=d\cdot (a_0,b_0)=(d\cdot a_0,d\cdot b_0)=LWE_s(d\cdot m_0)" />
</p>

令 <img src="https://latex.codecogs.com/svg.image?\mathbf{c_0}&space;=&space;(a_0,b_0)=LWE_s(m_0),\mathbf{c_1}&space;=&space;(a_1,b_1)=LWE_s(m_1)" title="\mathbf{c_0} = (a_0,b_0)=LWE_s(m_0),\mathbf{c_1} = (a_1,b_1)=LWE_s(m_1)" /> 分析一下加法操作和标量乘操作的噪声增长如下:
<p align="center">
<img src="https://latex.codecogs.com/svg.image?Err_s(\mathbf{c_0}&plus;\mathbf{c_1})=Err_s(\mathbf{c_0})&plus;Err_s(\mathbf{c_1})" title="Err_s(\mathbf{c_0}+\mathbf{c_1})=Err_s(\mathbf{c_0})+Err_s(\mathbf{c_1})" />
</p>
<p align="center">
<img src="https://latex.codecogs.com/svg.image?Err_s(d\cdot&space;\mathbf{c_0})=d\cdot&space;Err_s(\mathbf{c_0})" title="Err_s(d\cdot \mathbf{c_0})=d\cdot Err_s(\mathbf{c_0})" />
</p>
注意到上面的标量乘操作要求d是一个较小的数，否则噪声增长超出允许的上界就会出现解密错误。显然，一个自然的问题是: 我们可不可以构造更好的标量乘操作使得对任何大小的d都适用呢？
为了解决该问题，我们定义一个新的LWE加密形式:

<p align="center">
<img src="https://latex.codecogs.com/svg.image?RLWE'_s(m)=\left(RLWE_s(m),RLWE_s(Bm),RLWE_s(B^2m),\cdots,RLWE_s(B^{k-1}m)\right)" title="RLWE'_s(m)=\left(RLWE_s(m),RLWE_s(Bm),RLWE_s(B^2m),\cdots,RLWE_s(B^{B^{k-1}}m)\right)" />
</p>
<div>接着，对标量d进行基于B的扁平化操作 <img src="http://latex.codecogs.com/svg.latex?d=\sum_iB^id_i" title="http://latex.codecogs.com/svg.latex?d=\sum_iB^id_i" />。令 <img src="http://latex.codecogs.com/svg.latex?RLWE(B^im)=\mathbf{c}_i" title="http://latex.codecogs.com/svg.latex?RLWE(B^im)=\mathbf{c}_i" /> , 定义新的标量乘法运算如下: </div>

<p align="center">
<img src="http://latex.codecogs.com/svg.latex?d\odot&space;(\mathbf{c_0},\cdots,\mathbf{c_{k-1}})=\sum_id_i\cdot\mathbf{c_i}" title="http://latex.codecogs.com/svg.latex?d\odot (\mathbf{c_0},\cdots,\mathbf{c_{k-1}})=\sum_id_i\cdot\mathbf{c_i}" />
</p>
<div>容易发现，与旧的标量乘法操作 <img src="http://latex.codecogs.com/svg.latex?R\times&space;RLWE" title="http://latex.codecogs.com/svg.latex?R\times RLWE" /> 相比，新的标量乘法操作 <img src="http://latex.codecogs.com/svg.latex?\odot:&space;R\times&space;RLWE'\to&space;RLWE" title="http://latex.codecogs.com/svg.latex?\odot: R\times RLWE'\to RLWE" /> 拥有更好的噪声增长控制。</div>


### GSW 密码方案
#### 直觉
基于上述的RLWE和RLWE'方法，我们现在介绍(R)GSW。直觉上讲， RGSW构造如下:
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?RGSW_s(m)=(RLWE'_s(-s\cdot&space;m),RLWE'(m))" title="http://latex.codecogs.com/svg.latex?RGSW_s(m)=(RLWE'_s(-s\cdot m),RLWE'(m))" /></p>
<div>定义GSW乘法操作 <img src="http://latex.codecogs.com/svg.latex?\diamond:&space;RLWE\times&space;RGSW\to&space;RLWE" title="http://latex.codecogs.com/svg.latex?\diamond: RLWE\times RGSW\to RLWE" /> 如下</div>
 <p align="center">
  <img src="./fig/GSW.PNG" alt="animated" />
   </p>

#### 正式的构造
上述的是GSW的直觉式构造方法，GSW论文给出的实际构造有一些不同。


#### RGSW的噪声增长分析

