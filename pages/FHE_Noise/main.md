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

实践表明上面的分析方法是悲观的，即实际FHEW的解密错误概率通常比上式得到的概率要小的多。因此FHEW论文提供另外一种分析方法。这里我们避开分析单个FHE密文可解密的概率，转而分析两个(独立)FHE密文的噪声之和。假定单个FHE密文 <img src="https://latex.codecogs.com/svg.image?\mathbf{c_i}" title="\mathbf{c_i}" /> 的噪声满足 <img src="https://latex.codecogs.com/svg.image?|e_i|&space;=&space;|err(\mathbf{c_i})|<q/2t" title="|e_i| = |err(\mathbf{c_i})|<q/2t" /> 。
