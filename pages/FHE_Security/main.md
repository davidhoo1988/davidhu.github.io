# FHE的安全性
FHE是基于LWE和RLWE的构造。因此FHE安全性的基石就是LWE问题和RLWE问题。这里我们简要讨论一下LWE问题和RLWE问题。


## LWE问题的困难程度
首先，这里明确我们究竟要解决什么样的问题。 给定一系列的LWE样本<img src="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})=(\mathbf{A},\mathbf{A}\cdot&space;\mathbf{s}&plus;\mathbf{e})" title="https://latex.codecogs.com/svg.image?(\mathbf{A},\mathbf{c})=(\mathbf{A},\mathbf{A}\cdot \mathbf{s}+\mathbf{e})" />, 反推密钥向量 <img src="https://latex.codecogs.com/svg.image?\mathbf{s}" title="https://latex.codecogs.com/svg.image?\mathbf{s}" />
按目前学术界的认知，解决LWE问题主要是三条路径,即Dual Attack, Primal Attack 和 uSVP attack。Dual attack解决判定性LWE问题(Decision-LWE problem)的策略是找到一个‘短’向量使得

下面分别具体地讨论这三条路径。

### Dual Attack
