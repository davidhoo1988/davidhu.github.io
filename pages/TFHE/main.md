# TFHE方案介绍
这里介绍TFHE方案。它是对FHEW方案的拓展，特别是在改进GSW乘法和FHEW bootstrapping性能上做出了重要贡献。可以说TFHE是第三代FHE方案中性能最好的一个。

## 预备知识

## TFHE bootstrapping
### 基本原理
首先意识到 <img src="https://latex.codecogs.com/svg.image?R_{N,Q}" title="https://latex.codecogs.com/svg.image?R_{N,Q}" /> 的根构成了阶数为2N的乘性循环群(cyclic multiplicative group)
