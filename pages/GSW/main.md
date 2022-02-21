# GSW 介绍
GSW是公认的第一个第三代FHE方案。Craig Gentry, Amit Sahai 和 Brent Waters最先提出该方案，其成果发表在[CRYPTO13](https://eprint.iacr.org/2013/340)。GSW最主要的贡献是探索了一条新的构造同态乘法的路径。在第二代FHE方案中，同态乘法的噪声增加比同态加法要大的多。GSW提出新的构造可以更有效的控制同态乘法运算的噪声增长。作为代价，它的密文长度(矩阵形式)也要大的多。GSW方案是后续的FHEW方案和TFHE方案的基石。FHEW/TFHE正是借用GSW的同态乘法构造了新的bootstrapping算法。

## 预备知识
### RLWE 的基本结构和同态运算
首先，我们回顾RLWE(Learning With Errors over Rings)的代数构造。这里注意区别两个概念：加密(encrypt)和编码(encode)。 RLWE加密有如下形式：
<p align="center">
  <img src="https://latex.codecogs.com/svg.image?RLWE_s(\widetilde{m})=(a,as&plus;e&plus;\widetilde{m})" title="RLWE_s(\widetilde{m})=(a,as+e+\widetilde{m})" />
</p>
<div>这里, <img src="https://latex.codecogs.com/svg.image?\widetilde{m}\in&space;R_q=R/qR=\mathbb{Z}_q[X]/(X^n&plus;1)" title="\widetilde{m}\in R_q=R/qR=\mathbb{Z}_q[X]/(X^n+1)" /> 是编码之后的明文；<img src="https://latex.codecogs.com/svg.image?s\in&space;R=\mathbb{Z}[X]/(X^n&plus;1)" title="s\in R=\mathbb{Z}[X]/(X^n+1)" /> 是密钥。</div>

接着，我们回顾RLWE密文的同态加法操作。

最后，我们试着构造RLWE密文的同态乘法操作。

### GSW 密码方案
#### 直觉

#### 正式的构造
