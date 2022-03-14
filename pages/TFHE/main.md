# TFHE方案介绍
这里介绍TFHE方案。它是对FHEW方案的拓展，特别是在改进GSW乘法和FHEW bootstrapping性能上做出了重要贡献。可以说TFHE是第三代FHE方案中性能最好的一个。

## 预备知识
### Torus
可以证明Torus结构和正常的<img src="https://latex.codecogs.com/svg.image?\mathbb{Z}_p" title="https://latex.codecogs.com/svg.image?\mathbb{Z}_p" /> 结构是等价的。所以这篇文章里避免提及Torus从而使得TFHE方案的表述更简洁。

## TFHE bootstrapping
### 基本原理
首先意识到 <img src="https://latex.codecogs.com/svg.image?R_{N,Q}" title="https://latex.codecogs.com/svg.image?R_{N,Q}" /> 的根构成了阶数为2N的乘性循环群(cyclic multiplicative group) <img src="https://latex.codecogs.com/svg.image?\mathbb{G}=\{1,X,\cdots,X^{N-1},-1,\cdots,-X^{N-1}\}" title="https://latex.codecogs.com/svg.image?\mathbb{G}=\{1,X,\cdots,X^{N-1},-1,\cdots,-X^{N-1}\}" />。 TFHE bootstrapping的输入是一个LWE instance <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/t}(m)=(\mathbf{a},\mathbf{a}\cdot&space;\mathbf{s}&plus;e&plus;\frac{qm}{t})=(\mathbf{a},b)" title="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/t}(m)=(\mathbf{a},\mathbf{a}\cdot \mathbf{s}+e+\frac{qm}{t})=(\mathbf{a},b)" />, 为了表述方便这里假定q=2N。引入一个新概念，即旋转多项式(rotation polynomial) <img src="https://latex.codecogs.com/svg.image?rotP(X)=\frac{Q}{t}\cdot(1-\sum_{i=1}^{N-1}X^i)\in&space;R_Q" title="https://latex.codecogs.com/svg.image?rotP(X)=\frac{Q}{t}\cdot(1-\sum_{i=1}^{N-1}X^i)\in R_Q" />，初始化bootstrap的输入为 <img src="https://latex.codecogs.com/svg.image?acc=rotP\cdot&space;X^b\in&space;R_Q" title="https://latex.codecogs.com/svg.image?acc=rotP\cdot X^b\in R_Q" />, 接着对acc(同态地)乘以<img src="https://latex.codecogs.com/svg.image?X^{-a_i\cdot&space;s_i}" title="https://latex.codecogs.com/svg.image?X^{-a_i\cdot s_i}" /> 的密文（这个概念称之为blind rotation，在正式构造中再详细介绍）最终得到以下形式：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?RLWE(rotP\cdot&space;X^{b-\mathbf{a}\cdot&space;\mathbf{s}})=RLWE(rotP\cdot&space;X^{\frac{Q}{t}m&plus;e&space;\bmod&space;2N})" title="https://latex.codecogs.com/svg.image?RLWE(rotP\cdot X^{b-\mathbf{a}\cdot \mathbf{s}})=RLWE(rotP\cdot X^{\frac{Q}{t}m+e \bmod 2N})" />
</p>
<div>注意这里引入rotP的关键原因是：如果 <img src="https://latex.codecogs.com/svg.image?\frac{Q}{t}m&plus;e&space;\in&space;[0,N)&space;" title="https://latex.codecogs.com/svg.image?\frac{Q}{t}m+e \in [0,N) " />，那么多项式 <img src="https://latex.codecogs.com/svg.image?rotP\cdot&space;X^{\frac{Q}{t}m&plus;e&space;\bmod&space;2N}" title="https://latex.codecogs.com/svg.image?rotP\cdot X^{\frac{Q}{t}m+e \bmod 2N}" /> 的常数项一定是 <img src="https://latex.codecogs.com/svg.image?\frac{Q}{t}" title="https://latex.codecogs.com/svg.image?\frac{Q}{t}" />；如果 <img src="https://latex.codecogs.com/svg.image?\frac{Q}{t}m&plus;e&space;\in&space;[N,2N)" title="https://latex.codecogs.com/svg.image?\frac{Q}{t}m+e \in [N,2N)" />，则它的常数项一定是 <img src="https://latex.codecogs.com/svg.image?-\frac{Q}{t}" title="https://latex.codecogs.com/svg.image?-\frac{Q}{t}" />。如果我们可以同态地提取这个常数项，那么实际上就是同态地计算符号函数:</div>
<p align="center">
<img src="https://latex.codecogs.com/svg.image?sgn(\frac{Q}{t}m&plus;e)=\begin{cases}&space;&plus;1&&space;\text{&space;if&space;}&space;\frac{Q}{t}m&plus;e\in[0,N)&space;\\&space;-1&&space;\text{&space;if&space;}&space;\frac{Q}{t}m&plus;e\in[N,2N)&space;\end{cases}" title="https://latex.codecogs.com/svg.image?sgn(\frac{Q}{t}m+e)=\begin{cases} +1& \text{ if } \frac{Q}{t}m+e\in[0,N) \\ -1& \text{ if } \frac{Q}{t}m+e\in[N,2N) \end{cases}" />
 </p>
 
### 正式构造
这里我们以(同态)与非逻辑为例，正式介绍TFHE bootstrap。给定m0和m1的密文，经过bootstrap输出NAND(m0,m1)的密文，即：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE_\mathbf{s}^{q/4}(m_0)&plus;LWE_\mathbf{s}^{q/4}(m_1)\xrightarrow[]{bootstrap}&space;LWE_\mathbf{s}^{q/4}(\overline{m_0\wedge&space;m_1&space;})" title="https://latex.codecogs.com/svg.image?LWE_\mathbf{s}^{q/4}(m_0)+LWE_\mathbf{s}^{q/4}(m_1)\xrightarrow[]{bootstrap} LWE_\mathbf{s}^{q/4}(\overline{m_0\wedge m_1 })" />
</p>

首先,同态加法是容易做的，即<img src="https://latex.codecogs.com/svg.image?LWE^{t/q}_{\mathbf{s}}(m_0)&plus;LWE^{t/q}_{\mathbf{s}}(m_1)=LWE^{t/q}_{\mathbf{s}}(m_0&plus;m_1)" title="LWE^{t/q}_{\mathbf{s}}(m_0)+LWE^{t/q}_{\mathbf{s}}(m_1)=LWE^{t/q}_{\mathbf{s}}(m_0+m_1)" />。接下来的目标是同态地将<img src="https://latex.codecogs.com/svg.image?m_0&plus;m_1" title="m_0+m_1" /> 映射成 <img src="https://latex.codecogs.com/svg.image?\overline{m_0\wedge&space;m_1}" title="\overline{m_0\wedge m_1}" />，该映射可以用下面表格表示：

(a,b) | a+b  | NAND(a,b)
----  | ---- | ----
(0,0) | 0    | 1
(0,1) | 1    | 1
(1,0) | 1    | 1
(1,1) | 2    | 0

也就是说，这里的重难点是如何同态地构造这样的LUT函数f，可以把0映射成1，1映射成1，2映射成0。

### 噪声分析
bootstrap操作本身会引入额外噪声。为了保障TFHE bootstrap的正确性，一个关键的问题是分析bootstrap算法的噪声，使得噪声幅值大小在可控范围。
