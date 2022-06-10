# 蒙哥马利模乘

蒙哥马利模乘最主要的贡献就是提供了一种给定输入$T$，快速计算$TR^{-1}\bmod N (R>N)$的模约减方法。为了描述方便将该模约减方法记为$TR^{-1}\gets REDC(T)$。
现在定义一个整数$a\in \mathbb{Z}_{N}$ 的蒙哥马利形式为 <img src="https://latex.codecogs.com/svg.image?aR\in&space;\mathbb{Z}_N" title="https://latex.codecogs.com/svg.image?aR\in \mathbb{Z}_N" /> 。显然，一个整数$a$的蒙哥马利形式可以先算一次乘法$a\cdot R^2$ 最后算一次REDC得到，即:
$$aR = REDC(aR^2)$$


# 蒙哥马利模约减
现在讨论蒙哥马利模乘最重要的部分。
