# FHE噪声增长分析

## 预备知识

### 关于erf函数
误差函数(error function)是统计学中的一个重要函数。在FHE噪声分析中我们使用误差函数来估计噪声幅度在某个区间的概率。如果这个概率是可忽略的(negligible)，那么认定噪声只在该区间抖动变化。erf函数的定义如下:
<p align="center">
<img src="https://latex.codecogs.com/svg.image?erf(x)=\frac{1}{\sqrt{\pi}}\int_{-x}^{x}e^{-t^2}dt=\frac{2}{\sqrt{\pi}}\int_{0}^{x}e^{-t^2}dt" title="erf(x)=\frac{1}{\sqrt{\pi}}\int_{-x}^{x}e^{-t^2}dt=\frac{2}{\sqrt{\pi}}\int_{0}^{x}e^{-t^2}dt" />
 </p>
