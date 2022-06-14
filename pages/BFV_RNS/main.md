# RNS版本的BFV方案

这篇博文主要基于20116年的[BEHZ论文](https://eprint.iacr.org/2016/510)。目前所有实用的第二代FHE方案都是在RNS(Residue Number System, or Chinese Remainder Theorem Representation)上做的。这是因为RNS具有很高的计算并行度，并且可以利用计算机自带的整数类型(int32, int64)直接支持FHE方案需要的大数（$\mathbf{F}_{p}, \text{p is at least hundreds of bits long}$)
在正常数制下的BFV的介绍可以参考我之前的[一篇博文](https://github.com/davidhoo1988/davidhu.github.io/edit/gh-pages/pages/BFV/main.md)。


## 预备知识
### 模运算 Modular Arithemtic

### 中国剩余定理 Chinese Remainder Theorem
中国剩余定理CRT阐述了一个整数环的自同构的存在，即 <img src="https://latex.codecogs.com/svg.image?\mathbb{Z}_q&space;&space;\simeq&space;\prod_{i=1}^k\mathbb{Z}_{q_i}" title="https://latex.codecogs.com/svg.image?\mathbb{Z}_q \simeq \prod_{i=1}^k\mathbb{Z}_{q_i}" />。 CRT蕴含着一个非进位的数制系统(non-positional number system)，现在一般称之为余数系统(Residue Number System, RNS)。在RNS下，一个大整数(mod q)的模运算可以被拆分成k个小整数的模运算。通常每个小整数的模运算可以被绝大多数编程语言直接支持，因此大整数的模运算也可以被支持。

自然地，中国剩余定理可以推广到多项式环 <img src="https://latex.codecogs.com/svg.image?R_q&space;&space;\simeq&space;\prod_{i=1}^k&space;R_{q_i}" title="https://latex.codecogs.com/svg.image?R_q \simeq \prod_{i=1}^k R_{q_i}" />

### BFV方案
简单回顾BFV方案如下:

 <p align="center">
  <img src="fig/BFV_basic.PNG" alt="animated"/>
<

注意这里的公钥 $pk=(\mathbf{p}_0, \mathbf{p}_1)=([-(\mathbf{as}+\mathbf{e})]_q, \mathbf{a})$, 且 $\mathbf{p}_0+\mathbf{p}_1\mathbf{s}=-\mathbf{e}\approx 0$ 。据此，容易证明 $[ct(\mathbf{s})]_q = [ct[0]+ct[1]\mathbf{s}]_q = [\Delta [\mathbf{m}]_t + \mathbf{v}]_q$。 这里 $\mathbf{v}$ 是密文 $ct$ 的内置噪声。

更详尽的介绍可以参考[这篇博文](https://github.com/davidhoo1988/davidhu.github.io/edit/gh-pages/pages/BFV/main.md)

## RNS版本下的 BFV-Decryption


## RNS版本下的 BFV-Multiplication
