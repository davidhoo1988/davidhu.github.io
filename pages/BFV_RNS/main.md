# RNS版本的BFV方案

这篇博文主要基于20116年的[BEHZ论文](https://eprint.iacr.org/2016/510)。目前所有实用的第二代FHE方案都是在RNS(Residue Number System, or Chinese Remainder Theorem Representation)上做的。这是因为RNS具有很高的计算并行度，并且可以利用计算机自带的整数类型(int32, int64)直接支持FHE方案需要的大数（$\mathbf{F}_{p}, \text{p is at least hundreds of bits long}$)
在正常数制下的BFV的介绍可以参考我之前的[一篇博文](https://github.com/davidhoo1988/davidhu.github.io/edit/gh-pages/pages/BFV/main.md)。


## 预备知识

### 中国剩余定理 Chinese Remainder Theorem
中国剩余定理CRT阐述了一个整数环的自同构的存在，即 <img src="https://latex.codecogs.com/svg.image?\mathbb{Z}_q&space;&space;\simeq&space;\prod_{i=1}^k\mathbb{Z}_{q_i}" title="https://latex.codecogs.com/svg.image?\mathbb{Z}_q \simeq \prod_{i=1}^k\mathbb{Z}_{q_i}" />。 

自然地，中国剩余定理可以推广到多项式环 <img src="https://latex.codecogs.com/svg.image?R_q&space;&space;\simeq&space;\prod_{i=1}^k&space;R_{q_i}" title="https://latex.codecogs.com/svg.image?R_q \simeq \prod_{i=1}^k R_{q_i}" />

### BFV方案


## RNS版本下的 BFV-Decryption


## RNS版本下的 BFV-Multiplication
