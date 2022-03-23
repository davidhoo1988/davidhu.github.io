# FHE的安全性
FHE是基于LWE和RLWE的构造。因此FHE安全性的基石就是LWE问题和RLWE问题。这里我们简要讨论一下LWE问题和RLWE问题，主要参考了Rachel Player的[博士毕业论文](https://pure.royalholloway.ac.uk/portal/files/29983580/2018playerrphd.pdf)。


## LWE问题的困难程度
首先，明确我们究竟要解决什么样的问题。具体而言，我们有两个问题要解决，一个是LWE的搜索性问题(Search-LWE problem): 给定一系列的LWE样本<img src="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})=(\mathbf{A},\mathbf{A}\cdot&space;\mathbf{s}&plus;\mathbf{e})" title="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})=(\mathbf{A},\mathbf{A}\cdot \mathbf{s}+\mathbf{e})" />, 反推密钥向量 <img src="https://latex.codecogs.com/svg.image?\mathbf{s}" title="https://latex.codecogs.com/svg.image?\mathbf{s}" /> ; 另一个是LWE的判定性问题(Decision-LWE problem)：给定 <img src="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})\in&space;\mathbb{Z}_q^{mn}\times&space;\mathbb{Z}_q^{m}" title="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})\in \mathbb{Z}_q^{mn}\times \mathbb{Z}_q^{m}" />, 要求判定它究竟是来自LWE样本还是来自随机均匀分布。
按目前学术界的认知，解决LWE问题主要是三条路径，即Dual attack, Primal attack 和 uSVP attack。

Dual attack解决判定性LWE问题(Decision-LWE problem)的策略是找到一个‘短’向量使得 <img src="https://latex.codecogs.com/svg.image?\mathbf{v}\cdot&space;\mathbf{A}=\mathbf{0}" title="https://latex.codecogs.com/svg.image?\mathbf{v}\cdot \mathbf{A}=\mathbf{0}" /> ；Primal attack解决搜索性LWE问题的策略是找到一个‘短’向量 <img src="https://latex.codecogs.com/svg.image?\mathbf{e}" title="https://latex.codecogs.com/svg.image?\mathbf{e}" /> 使得 <img src="https://latex.codecogs.com/svg.image?\mathbf{Ax}=\mathbf{c}-\mathbf{e}" title="https://latex.codecogs.com/svg.image?\mathbf{Ax}=\mathbf{c}-\mathbf{e}" />。

下面分别具体地讨论这三条路径。

### Dual Attack


## RLWE问题的困难程度


## LWE问题困难程度评估器(LWE Estimator)
这里介绍一款目前最流行的评估LWE问题困难的实用方法---LWE Estimator。它是基于SageMath开发的开源软件，基本囊括了LWE问题的各式攻击方式。
