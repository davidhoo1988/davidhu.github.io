# GSW 介绍
GSW是公认的第一个第三代FHE方案。最初的论文发表在[CRYPTO13](https://eprint.iacr.org/2013/340)。GSW最主要的贡献是探索了一条新的构造同态乘法的路径。在第二代FHE方案中，同态乘法的噪声增加比同态加法要大的多。GSW提出新的构造可以更有效的控制同态乘法运算的噪声增长。作为代价，它的密文长度(矩阵形式)也要大的多。GSW方案是后续的FHEW方案和TFHE方案的基础。FHEW/TFHE正是借用GSW的同态乘法构造了新的bootstrapping算法。

## 预备知识
### RLWE 的基本结构和同态运算
首先，我们回顾RLWE(Learning With Errors over Rings)的代数构造。


接着，我们回顾RLWE密文的同态加法操作。

最后，我们试着构造RLWE密文的同态乘法操作。

### GSW 密码方案
