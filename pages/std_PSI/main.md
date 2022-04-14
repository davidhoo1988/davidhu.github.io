
# 标准PSI
这里正式的描述什么是标准PSI。计算双方各种拥有集合，记为C和S。在PSI协议计算过程中，这些集合内部信息都不允许透露，计算结束后双方只知道集合交集部分，其他关于集合的信息一概不知道。该过程可以用下图表示：
   <p align="center">
  <img src="fig/PSI_overview.png" alt="animated" />
   </p>

   <p align="center">
  <img src="fig/OPRF.png" alt="animated" />
   </p>
   <p align="center">
  <img src="fig/OPRF_dh.png" alt="animated" />
   </p>   
   
   <p align="center">
  <img src="fig/PSI_oprf.png" alt="animated" />
   </p>
   
   
  PSI的构造有很多，大致上可以分成DH-based, circuit-based, OT-based。 本篇文章介绍DH-based，也就是基于Diffie-Hellman假设的群结构。该方法首先于1986年被提出。我们按这里的讲座视频来详细介绍这种方法。大致上，需要进行两步。首先需要基于DH构造OPRF(oblivious pseudo-random function);接着利用OPRF最终构造PSI。
  
  ## Oblivious Pseudo-Random Function (OPRF) 
