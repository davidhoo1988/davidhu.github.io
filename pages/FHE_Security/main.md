# FHE的安全性
FHE是基于LWE和RLWE的构造。因此FHE安全性的基石就是LWE问题和RLWE问题。这里我们简要讨论一下LWE问题和RLWE问题，主要参考了Rachel Player的[博士毕业论文](https://pure.royalholloway.ac.uk/portal/files/29983580/2018playerrphd.pdf)。


## LWE问题的困难程度
首先，明确我们究竟要解决什么样的问题。具体而言，我们有两个问题要解决，一个是LWE的搜索性问题(Search-LWE problem): 给定一系列的LWE样本<img src="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})=(\mathbf{A},\mathbf{A}\cdot&space;\mathbf{s}&plus;\mathbf{e})" title="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})=(\mathbf{A},\mathbf{A}\cdot \mathbf{s}+\mathbf{e})" />, 反推密钥向量 <img src="https://latex.codecogs.com/svg.image?\mathbf{s}" title="https://latex.codecogs.com/svg.image?\mathbf{s}" /> ; 另一个是LWE的判定性问题(Decision-LWE problem)：给定 <img src="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})\in&space;\mathbb{Z}_q^{mn}\times&space;\mathbb{Z}_q^{m}" title="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})\in \mathbb{Z}_q^{mn}\times \mathbb{Z}_q^{m}" />, 要求判定它究竟是来自LWE样本还是来自随机均匀分布。
按目前学术界的认知，解决LWE问题主要是三条路径，即Dual attack, Primal attack 和 uSVP attack。

Dual attack解决判定性LWE问题(Decision-LWE problem)的策略是找到一个‘短’向量使得 <img src="https://latex.codecogs.com/svg.image?\mathbf{v}\cdot&space;\mathbf{A}=\mathbf{0}" title="https://latex.codecogs.com/svg.image?\mathbf{v}\cdot \mathbf{A}=\mathbf{0}" /> ；Primal attack解决搜索性LWE问题的策略是找到一个‘短’向量 <img src="https://latex.codecogs.com/svg.image?\mathbf{e}" title="https://latex.codecogs.com/svg.image?\mathbf{e}" /> 使得 <img src="https://latex.codecogs.com/svg.image?\mathbf{Ax}=\mathbf{c}-\mathbf{e}" title="https://latex.codecogs.com/svg.image?\mathbf{Ax}=\mathbf{c}-\mathbf{e}" />。

下面分别具体地讨论这三条路径。

### Dual Attack
再次申明这里的问题是：给定m个LWE采样 <img src="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})&space;\text{&space;with&space;}&space;\mathbf{c}=\mathbf{A}\mathbf{s}&plus;\mathbf{e}" title="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c}) \text{ with } \mathbf{c}=\mathbf{A}\mathbf{s}+\mathbf{e}" /> ，且 <img src="https://latex.codecogs.com/svg.image?\mathbf{e}\sim&space;\mathcal{N}(0,\sigma^2)" title="https://latex.codecogs.com/svg.image?\mathbf{e}\sim \mathcal{N}(0,\sigma^2)" /> 。现在需要将他们和满足均匀分布的 <img src="https://latex.codecogs.com/svg.image?(\mathbf{A'},\mathbf{c'})" title="https://latex.codecogs.com/svg.image?(\mathbf{A'},\mathbf{c'})" /> 区分开来。

Dual attack的攻击思路是寻找这样的短向量 <img src="https://latex.codecogs.com/svg.image?\mathbf{v}\in&space;L" title="https://latex.codecogs.com/svg.image?\mathbf{v}\in L" /> 满足
<p align="center">
<img src="https://latex.codecogs.com/svg.image?L=\{\mathbf{v}\in&space;\mathbb{Z}_q^m|\mathbf{v}\mathbf{A}=\mathbf{0}&space;\bmod&space;q\}" title="https://latex.codecogs.com/svg.image?L=\{\mathbf{v}\in \mathbb{Z}_q^m|\mathbf{v}\mathbf{A}=\mathbf{0} \bmod q\}" />
</p>
<div>容易看出这其实就是SIS(short integer solution problem)问题。接着，做内积运算得 <img src="https://latex.codecogs.com/svg.image?\langle\mathbf{v},\mathbf{c}\rangle&space;=&space;\langle\mathbf{v},\mathbf{e}\rangle&space;\sim&space;\mathcal{N}(0,v_i^2\sigma^2)" title="https://latex.codecogs.com/svg.image?\langle\mathbf{v},\mathbf{c}\rangle = \langle\mathbf{v},\mathbf{e}\rangle \sim \mathcal{N}(0,v_i^2\sigma^2)" /></div>

  
## RLWE问题的困难程度


## LWE问题困难程度评估器(LWE Estimator)
这里介绍一款目前最流行的评估LWE问题困难的实用方法---LWE Estimator。它是基于SageMath开发的开源软件，基本囊括了LWE问题的各式攻击方式。
