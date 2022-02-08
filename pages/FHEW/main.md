## FHEW介绍

FHEW是开启第三代FHE的标志性方案。该方案主要是Leo Ducas he Daniele Micciancio在2014年提出并设计。原始论文可以参考[这里](https://eprint.iacr.org/2014/816.pdf)。与第二代FHE方案相比，bootstrapping的性能得到大幅度提升，在常见的台式机平台上速度可以达到毫秒级别；但同时因为缺少第二代FHE的SIMD特性，FHEW只能处理若干比特的加法和乘法操作，也就是说同态乘法的性能较差。

### 预备知识
#### Learning With Errors (LWE) 对称加密
这里将对明文m的LWE加密标注成![equation](https://latex.codecogs.com/svg.image?LWE_{\vec{s}}(m)=(\vec{a},b)). 其中 <img src="https://latex.codecogs.com/svg.image?\mathbf{b}=<\mathbf{a},\mathbf{s}>&plus;e&plus;\frac{q}{t}m&space;\bmod&space;q" title="\mathbf{b}=<\mathbf{a},\mathbf{s}>+e+\frac{q}{t}m \bmod q" />

已知密钥![equation](https://latex.codecogs.com/svg.image?\vec{s})，则可以对![equation](https://latex.codecogs.com/svg.image?LWE_{\vec{s}}(m)=(\vec{a},b))做解密操作，算法如下：

<p align="center">
<img src="https://latex.codecogs.com/svg.image?\left\lfloor&space;t(b-\mathbf{a}\cdot&space;\mathbf{s})/q\right\rceil&space;\bmod&space;t&space;=&space;\left\lfloor&space;\frac{t}{q}\cdot&space;(\frac{q}{t}m&plus;e)\right\rceil&space;=&space;\left\lfloor&space;m&plus;\frac{t}{q}e\right\rceil&space;=&space;m\bmod&space;t" title="\left\lfloor t(b-\mathbf{a}\cdot \mathbf{s})/q\right\rceil \bmod t = \left\lfloor \frac{t}{q}\cdot (\frac{q}{t}m+e)\right\rceil = \left\lfloor m+\frac{t}{q}e\right\rceil = m\bmod t" />
</p>

#### Modular Switching 和 Key Switching
这里引入FHE方案中的两个重要基本操作Modular Switching(M.S.) 和 Key Switching(K.S.)。它们会反复地出现在FHE系列的文章中。我们不加证明的使用如下结论：

### FHEW顶层逻辑结构

### 同态与门逻辑(Homomorphic AND gate)

### 同态累加器(Homomorphic Accumulator)
