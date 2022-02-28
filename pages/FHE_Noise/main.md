# FHE噪声增长分析

## 预备知识

### 关于erf函数
误差函数(error function)是统计学中的一个重要函数。在FHE噪声分析中我们使用误差函数来估计噪声幅度在某个区间的概率。如果这个概率非常接近1，那么认定噪声只在该区间抖动变化。erf函数的定义如下:
<p align="center">
<img src="https://latex.codecogs.com/svg.image?erf(x)=\frac{1}{\sqrt{\pi}}\int_{-x}^{x}e^{-t^2}dt=\frac{2}{\sqrt{\pi}}\int_{0}^{x}e^{-t^2}dt" title="erf(x)=\frac{1}{\sqrt{\pi}}\int_{-x}^{x}e^{-t^2}dt=\frac{2}{\sqrt{\pi}}\int_{0}^{x}e^{-t^2}dt" />
 </p>
 
引入erf函数的一个重要性质，该性质沟通了erf函数和高斯分布:
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\Phi(x)=\frac{1}{2}&plus;\frac{1}{2}\text{&space;erf}(\frac{x}{\sqrt{2}})&space;" title="\Phi(x)=\frac{1}{2}+\frac{1}{2}\text{ erf}(\frac{x}{\sqrt{2}}) " />
 </p>
 
 对上式做一些简单变换，可得：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\text{&space;erf}(\frac{x}{\sqrt{2}})&space;=&space;2(\Phi(x)-\Phi(0))" title="\text{ erf}(\frac{x}{\sqrt{2}}) = 2(\Phi(x)-\Phi(0))" />
</p>
也就是说，一个服从高斯分布的随机变量出现在区间[-x,+x]的概率可以用erf函数直接计算。

### 高斯分布的叠加性

## FHE密文被正确解密的概率
我们回到FHE噪声分析的核心问题，那就是“给定FHE密文噪声分布(通常是均值为0，方差为 <img src="https://latex.codecogs.com/svg.image?\sigma^2" title="\sigma^2" /> 的高斯分布)，那么该密文可以被正确解密的概率是多少？” 注意这里正确解密的含义是 <img src="https://latex.codecogs.com/svg.image?|e|<\frac{q}{2t}" title="|e|<\frac{q}{2t}" /> 。这就表明噪声随机变量e应该以大概率出现在区间 <img src="https://latex.codecogs.com/svg.image?[-\frac{q}{2t},&space;\frac{q}{2t}]" title="[-\frac{q}{2t}, \frac{q}{2t}]" /> 。等价的表述方式是e落在区间 <img src="https://latex.codecogs.com/svg.image?[-\frac{q}{2t},&space;\frac{q}{2t}]" title="[-\frac{q}{2t}, \frac{q}{2t}]" /> 之外的概率是可忽略的(例如概率为 <img src="https://latex.codecogs.com/svg.image?2^{-30}" title="2^{-30}" /> ):
<p align="center">
<img src="https://latex.codecogs.com/svg.image?1-\text{erf}(\frac{q}{2t\sigma\cdot&space;\sqrt{2}})\leq&space;2^{-i}" title="1-\text{erf}(\frac{q}{2t\sigma\cdot \sqrt{2}})\leq 2^{-i}" /> </p>

实践表明上面的分析方法是悲观的，即实际FHEW的解密错误概率通常比上式得到的概率要小的多。因此FHEW论文提供另外一种分析方法。这里我们避开分析单个FHE密文可解密的概率，转而分析两个(独立)FHE密文的噪声之和。假定单个FHE密文 <img src="https://latex.codecogs.com/svg.image?\mathbf{c_i}" title="\mathbf{c_i}" /> 的噪声满足 <img src="https://latex.codecogs.com/svg.image?|e_i|&space;=&space;|err(\mathbf{c_i})|<q/2t" title="|e_i| = |err(\mathbf{c_i})|<q/2t" /> 。那么两个独立FHE密文的噪声之和应满足 <img src="https://latex.codecogs.com/svg.image?|e_1&plus;e_2|=|err(\mathbf{c_1})&plus;err(\mathbf{c_2})|<q/t" title="|e_1+e_2|=|err(\mathbf{c_1})+err(\mathbf{c_2})|<q/t" /> 。因此FHEW用下式来估算解密的出错概率：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?1-\text{erf}(\frac{q}{t\sigma\cdot&space;\sqrt{2}})\leq&space;2^{-i}" title="1-\text{erf}(\frac{q}{t\sigma\cdot \sqrt{2}})\leq 2^{-i}" /> </p>

## 密钥变换/模数变换对FHEW密文解密错误率的影响

### 模数变换
给定一个LWE密文 <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{Q/t}(m)" title="LWE_{\mathbf{s}}^{Q/t}(m)" /> , 定义LWE模数变换如下：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?ModSwitch(\mathbf{a},b)=(\mathbf{a'},b')=((\lfloor&space;qa_0/Q\rceil,\cdots,\lfloor&space;qa_{n-1}/Q\rceil),&space;\lfloor&space;qb/Q\rceil&space;)" title="ModSwitch(\mathbf{a},b)=(\mathbf{a'},b')=((\lfloor qa_0/Q\rceil,\cdots,\lfloor qa_{n-1}/Q\rceil), \lfloor qb/Q\rceil )" /></p>

引理: 那么 <img src="https://latex.codecogs.com/svg.image?ModSwitch(\mathbf{a},b)" title="ModSwitch(\mathbf{a},b)" /> 输出的LWE密文(高斯)噪声方差为 <img src="https://latex.codecogs.com/svg.image?\sqrt{(q\sigma/Q)^2&space;&plus;&space;(||\mathbf{s}||^2&plus;1)/12}" title="\sqrt{(q\sigma/Q)^2 + (||\mathbf{s}||^2+1)/12}" />, 这里
<img src="https://latex.codecogs.com/svg.image?||s||=\sqrt{\sum_{i=0}^{n-1}s_i^2}" title="||s||=\sqrt{\sum_{i=0}^{n-1}s_i^2}" />

*证明*