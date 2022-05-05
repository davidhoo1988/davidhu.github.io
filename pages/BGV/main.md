# BGV方案
第二代FHE主要是有BGV,BFV,和CKKS组成。这里介绍BGV。BGV最早有Zvika Brakerski, Craig Gentry和Vinod Vaikuntanathan于2012年在[这篇文章](https://eprint.iacr.org/2011/277)首次提出。
BGV最重要的贡献是提出了模数转换(Modular Switching)技术,有效的控制了同态运算带来的密文噪声增加，从而实现了构造Leveled FHE：即这样的FHE可以实现给定计算深度的同态计算任务。但是，BGV并不能实现任意深度下的同态计算，不能算做真正意义上的FHE。

## 预备知识

### 推广的LWE

### LWE假设的一个变种
这里引入一个重要的假设：

若 <img src="https://latex.codecogs.com/svg.image?a\overset{\$}{\leftarrow}R_q,&space;s\overset{\$}{\leftarrow}\chi" title="https://latex.codecogs.com/svg.image?a\overset{\$}{\leftarrow}R_q, s\overset{\$}{\leftarrow}\chi" /> ，那么 <img src="https://latex.codecogs.com/svg.image?(a,as&plus;2e)\approx_c&space;(a,b)" title="https://latex.codecogs.com/svg.image?(a,as+2e)\approx_c (a,b)" /> 。 这里 <img src="https://latex.codecogs.com/svg.image?b\overset{\$}{\leftarrow}R_q" title="https://latex.codecogs.com/svg.image?b\overset{\$}{\leftarrow}R_q" /> 。
