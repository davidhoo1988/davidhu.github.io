
# 标准PSI
这里正式的描述什么是标准PSI。计算双方各种拥有集合，记为C和S。在PSI协议计算过程中，这些集合内部信息都不允许透露，计算结束后双方只知道集合交集部分，其他关于集合的信息一概不知道。该过程可以用下图表示：
   <p align="center">
  <img src="fig/PSI_overview.png" alt="animated" />
   </p>


   
  PSI的构造有很多，大致上可以分成DH-based, circuit-based, OT-based。 本篇文章介绍DH-based，也就是基于Diffie-Hellman假设的群结构。该方法首先于1986年被提出。我们按这里的讲座视频来详细介绍这种方法。大致上，需要进行两步。首先需要基于DH构造OPRF(oblivious pseudo-random function);接着利用OPRF最终构造PSI。
  
  ## Oblivious Pseudo-Random Function (OPRF) 
  我们知道PRF是伪随机函数，它的输出和真随机分布在多项式时间内不可区分；在此基础上引入OPRF。OPRF和PRF的主要区别是需要两方参与PRF的计算。Alice拥有函数的输入x, Bob拥有密钥k，Alice和Bob合作得到 <img src="https://latex.codecogs.com/svg.image?F_K(x)" title="https://latex.codecogs.com/svg.image?F_K(x)" />，但是Alice不会向Bob泄露自己的x，同样地，Bob也不会向Alice泄露自己的k。整个OPRF计算流程可以用下图表示：
   <p align="center">
  <img src="fig/OPRF.png" alt="animated" />
   </p>
   <p align="center">
  <img src="fig/OPRF_dh.png" alt="animated" />
   </p>   
   
  ## 通过OPRF构造PSI
  
   <p align="center">
  <img src="fig/PSI_oprf.png" alt="animated" />
   </p>
   
   
   ## 通讯复杂度优化
   
   ## 代码实现
   
