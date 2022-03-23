# FHE的安全性
FHE是基于LWE和RLWE的构造。因此FHE安全性的基石就是LWE问题和RLWE问题。这里我们简要讨论一下LWE问题和RLWE问题。


## LWE问题的困难程度
首先，这里明确我们究竟要解决什么样的问题。具体而言，我们有两个问题要解决，一个是LWE的搜索性问题(Search-LWE problem): 给定一系列的LWE样本<img src="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})=(\mathbf{A},\mathbf{A}\cdot&space;\mathbf{s}&plus;\mathbf{e})" title="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})=(\mathbf{A},\mathbf{A}\cdot \mathbf{s}+\mathbf{e})" />, 反推密钥向量 <img src="https://latex.codecogs.com/svg.image?\mathbf{s}" title="https://latex.codecogs.com/svg.image?\mathbf{s}" /> ; 另一个是LWE的判定性问题(Decision-LWE problem)。
按目前学术界的认知，解决LWE问题主要是三条路径,即Dual Attack, Primal Attack 和 uSVP attack。

Dual attack解决判定性LWE问题(Decision-LWE problem)的策略是找到一个‘短’向量使得 <img src="https://latex.codecogs.com/svg.image?\mathbf{v}\cdot&space;\mathbf{A}=\mathbf{0}" title="https://latex.codecogs.com/svg.image?\mathbf{v}\cdot \mathbf{A}=\mathbf{0}" /> ；

下面分别具体地讨论这三条路径。

### Dual Attack
