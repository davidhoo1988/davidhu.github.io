# 通过Oblivious Polynomial Evaluation 构造标准PSI
通过OPE实现PSI是一种重要方式。这里我们首先介绍OPE并给出基于FHE的OPE实现，最后利用OPE构造PSI。

## Oblivious Polynomial Evaluation (OPE)
### Membership Test
首先讨论如何利用OPE来解决Membership Test问题，即Client需要检测自己集合的元素 <img src="https://latex.codecogs.com/svg.image?y\in&space;X" title="https://latex.codecogs.com/svg.image?y\in X" /> 是否在Server的集合X中，除此之外Server和Client都不应探知任何其他信息。下图描述了整个方法：
  <p align="center">
  <img src="fig/membership_test_ope.png" alt="animated" />
  </p>
  
  注意这里Bob得到的结果：如果 <img src="https://latex.codecogs.com/svg.image?y\in&space;X" title="https://latex.codecogs.com/svg.image?y\in X" />，那么Bob得到0；如果 <img src="https://latex.codecogs.com/svg.image?y\notin&space;X" title="https://latex.codecogs.com/svg.image?y\notin X" />, 那么Bob得到一个随机数，因此不会泄露集合X内其他元素的信息。
  
 另一方面，上述方法最大的挑战是如何计算[z]。因为FHE支持的乘法次数总是有限的(为了保障FHE的运算速度足够快，在实际应用里常常不考虑使用bootstrap.。FHE乘法深度的典型值为6)，而计算[z]需要的乘法深度为 <img src="https://latex.codecogs.com/svg.image?log|X|=logN" title="https://latex.codecogs.com/svg.image?log|X|=logN" /> 。如何优化[z]的乘法计算深度是核心问题。
 
 
 现在我们讨论如何解决它,即给定[y]，如何使用尽可能少的乘法深度算出 <img src="https://latex.codecogs.com/svg.image?[z]=a_n[y^N]&plus;\cdots&plus;a_1[y]&plus;a_0" title="https://latex.codecogs.com/svg.image?[z]=a_n[y^N]+\cdots+a_1[y]+a_0" />。
 
 方法一：给定唯一的[y]，计算 <img src="https://latex.codecogs.com/svg.image?[y^2]=[y][y],&space;[y^4]=[y^2][y^2],&space;\cdots" title="https://latex.codecogs.com/svg.image?[y^2]=[y][y], [y^4]=[y^2][y^2], \cdots" /> 。按此方法，容易得出算乘法深度为 <img src="https://latex.codecogs.com/svg.image?logN" title="https://latex.codecogs.com/svg.image?logN" /> 。
 
 方法二：给定所有的 <img src="https://latex.codecogs.com/svg.image?[y],[y^2],\cdots,[y^N]" title="https://latex.codecogs.com/svg.image?[y],[y^2],\cdots,[y^N]" /> 。按此方法，可知无需做乘法即可算得[z], 其乘法深度为0。方法二的主要问题是通讯代价很大，需要 <img src="https://latex.codecogs.com/svg.image?\mathcal{O}(N)" title="https://latex.codecogs.com/svg.image?\mathcal{O}(N)" /> 。
 
 方法三: 给定所有的2的幂次项，即 <img src="https://latex.codecogs.com/svg.image?[y],[y^2],[y^4],\cdots,[y^{2^i}],\cdots,[y^{2^{logN}}]" title="https://latex.codecogs.com/svg.image?[y],[y^2],[y^4],\cdots,[y^{2^i}],\cdots,[y^{2^{logN}}]" /> 。容易推出计算[z]需要的计算深度为 <img src="https://latex.codecogs.com/svg.image?loglogN" title="https://latex.codecogs.com/svg.image?loglogN" /> ，通讯代价为 <img src="https://latex.codecogs.com/svg.image?\mathcal{O}(logN)" title="https://latex.codecogs.com/svg.image?\mathcal{O}(logN)" /> 。
 
至此，我们利用方法三例化Private Membership Test, 如下图所示:

  <p align="center">
  <img src="fig/membership_test_ope2.png" alt="animated" width: "50%" />
  </p>
