# TFHE方案介绍
这里介绍TFHE方案。它是对FHEW方案的拓展，特别是在改进GSW乘法和FHEW bootstrapping性能上做出了重要贡献。可以说TFHE是第三代FHE方案中性能最好的一个。

## 预备知识
# Torus

## TFHE bootstrapping
### 基本原理
首先意识到 <img src="https://latex.codecogs.com/svg.image?R_{N,Q}" title="https://latex.codecogs.com/svg.image?R_{N,Q}" /> 的根构成了阶数为2N的乘性循环群(cyclic multiplicative group) <img src="https://latex.codecogs.com/svg.image?\mathbb{G}=\{1,X,\cdots,X^{N-1},-1,\cdots,-X^{N-1}\}" title="https://latex.codecogs.com/svg.image?\mathbb{G}=\{1,X,\cdots,X^{N-1},-1,\cdots,-X^{N-1}\}" />。 TFHE bootstrapping的输入是一个LWE instance <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/t}(m)=(\mathbf{a},\mathbf{a}\cdot&space;\mathbf{s}&plus;e&plus;\frac{qm}{t})=(\mathbf{a},b)" title="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/t}(m)=(\mathbf{a},\mathbf{a}\cdot \mathbf{s}+e+\frac{qm}{t})=(\mathbf{a},b)" />, 为了表述方便这里假定q=2N。引入一个新概念，即旋转多项式(rotation polynomial) <img src="https://latex.codecogs.com/svg.image?rotP(X)=\frac{Q}{t}\cdot(1-\sum_{i=1}^{N-1}X^i)\in&space;R_Q" title="https://latex.codecogs.com/svg.image?rotP(X)=\frac{Q}{t}\cdot(1-\sum_{i=1}^{N-1}X^i)\in R_Q" />

### 正式构造


### 噪声分析
