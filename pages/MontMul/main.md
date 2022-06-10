# 蒙哥马利模乘

蒙哥马利模乘最主要的贡献就是提供了一种给定输入$T$，快速计算$TR^{-1}\bmod N (R>N)$的模约减方法。为了描述方便将该模约减方法记为$TR^{-1}\gets REDC(T)$。
现在定义一个整数$a\in \mathbb{Z}_{N}$ 的蒙哥马利形式为 <img src="https://latex.codecogs.com/svg.image?aR\in&space;\mathbb{Z}_N" title="https://latex.codecogs.com/svg.image?aR\in \mathbb{Z}_N" /> 。显然，一个整数$a$的蒙哥马利形式可以先算一次乘法$a\cdot R^2$ 最后算一次REDC得到，即:
$$aR = REDC(aR^2)$$

现在考虑如何计算两个整数$a$和$b$的模乘，即$ab\bmod N$。可以分三步进行计算。
1. 将输入转成蒙哥马利形式，即 $aR=REDC(aR^2), bR=REDC(bR^2)$
2. 做一次标准乘法得$abR^2=aR\cdot bR$
3. 最后做一次REDC得 $abR=REDC(abR^2)$

注意上面三个步骤返回的是蒙哥马利形式的$ab$，即$abR$。若需要转换成正常形式的$ab$，需要再做一次REDC得$ab=REDC(abR)$。

# 蒙哥马利模约减
现在讨论蒙哥马利模乘最重要的部分REDC。下面给出REDC的算法描述

