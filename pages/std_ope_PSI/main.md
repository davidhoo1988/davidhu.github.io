# 通过Oblivious Polynomial Evaluation 构造标准PSI
通过OPE实现PSI是一种重要方式。这里我们首先介绍OPE并给出基于FHE的OPE实现，最后利用OPE构造PSI。

## Oblivious Polynomial Evaluation (OPE)
### Membership Test
首先讨论如何利用OPE来解决Membership Test问题，即Client需要检测自己集合的元素 <img src="https://latex.codecogs.com/svg.image?y\in&space;X" title="https://latex.codecogs.com/svg.image?y\in X" /> 是否在Server的集合X中，除此之外Server和Client都不应探知任何其他信息。下图描述了整个方法：
