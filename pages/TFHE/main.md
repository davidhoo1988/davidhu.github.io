# TFHE方案介绍
这里介绍TFHE方案。它是对FHEW方案的拓展，特别是在改进GSW乘法和FHEW bootstrapping性能上做出了重要贡献。可以说TFHE是第三代FHE方案中性能最好的一个。

## 预备知识
# Torus

## TFHE bootstrapping
### 基本原理
首先意识到 <img src="https://latex.codecogs.com/svg.image?R_{N,Q}" title="https://latex.codecogs.com/svg.image?R_{N,Q}" /> 的根构成了阶数为2N的乘性循环群(cyclic multiplicative group) <img src="https://latex.codecogs.com/svg.image?\mathbb{G}=\{1,X,\cdots,X^{N-1},-1,\cdots,-X^{N-1}\}" title="https://latex.codecogs.com/svg.image?\mathbb{G}=\{1,X,\cdots,X^{N-1},-1,\cdots,-X^{N-1}\}" />。 TFHE bootstrapping的输入是一个LWE instance <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/t}(m)=(\mathbf{a},\mathbf{a}\cdot&space;\mathbf{s}&plus;e&plus;\frac{qm}{t})=(\mathbf{a},b)" title="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/t}(m)=(\mathbf{a},\mathbf{a}\cdot \mathbf{s}+e+\frac{qm}{t})=(\mathbf{a},b)" />, 为了表述方便这里假定q=2N。引入一个新概念，即旋转多项式(rotation polynomial) <img src="https://latex.codecogs.com/svg.image?rotP(X)=\frac{Q}{t}\cdot(1-\sum_{i=1}^{N-1}X^i)\in&space;R_Q" title="https://latex.codecogs.com/svg.image?rotP(X)=\frac{Q}{t}\cdot(1-\sum_{i=1}^{N-1}X^i)\in R_Q" />，初始化bootstrap的输入为 <img src="https://latex.codecogs.com/svg.image?acc=rotP\cdot&space;X^b\in&space;R_Q" title="https://latex.codecogs.com/svg.image?acc=rotP\cdot X^b\in R_Q" />, 接着对acc(同态地)乘以<img src="https://latex.codecogs.com/svg.image?X^{-a_i\cdot&space;s_i}" title="https://latex.codecogs.com/svg.image?X^{-a_i\cdot s_i}" /> 的密文（这个概念称之为blind rotation，在正式构造中再详细介绍）最终得到以下形式：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?RLWE(rotP\cdot&space;X^{b-\mathbf{a}\cdot&space;\mathbf{s}})=RLWE(rotP\cdot&space;X^{\frac{Q}{t}m&plus;e&space;\bmod&space;2N})" title="https://latex.codecogs.com/svg.image?RLWE(rotP\cdot X^{b-\mathbf{a}\cdot \mathbf{s}})=RLWE(rotP\cdot X^{\frac{Q}{t}m+e \bmod 2N})" />
</p>
注意这里引入rotP的关键原因是：如果 <img src="https://latex.codecogs.com/svg.image?\frac{Q}{t}m&plus;e&space;\in&space;[0,N)&space;" title="https://latex.codecogs.com/svg.image?\frac{Q}{t}m+e \in [0,N) " />，那么多项式 <img src="https://latex.codecogs.com/svg.image?rotP\cdot&space;X^{\frac{Q}{t}m&plus;e&space;\bmod&space;2N}" title="https://latex.codecogs.com/svg.image?rotP\cdot X^{\frac{Q}{t}m+e \bmod 2N}" /> 的常数项一定是 <img src="https://latex.codecogs.com/svg.image?\frac{Q}{t}" title="https://latex.codecogs.com/svg.image?\frac{Q}{t}" />；如果 <img src="https://latex.codecogs.com/svg.image?\frac{Q}{t}m&plus;e&space;\in&space;[N,2N)" title="https://latex.codecogs.com/svg.image?\frac{Q}{t}m+e \in [N,2N)" />，则它的常数项一定是 <img src="https://latex.codecogs.com/svg.image?-\frac{Q}{t}" title="https://latex.codecogs.com/svg.image?-\frac{Q}{t}" />

### 正式构造


### 噪声分析
