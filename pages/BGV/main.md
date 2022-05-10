# BGV方案
第二代FHE主要是有BGV,BFV,和CKKS组成。这里介绍BGV。BGV最早有Zvika Brakerski, Craig Gentry和Vinod Vaikuntanathan于2012年在[这篇文章](https://eprint.iacr.org/2011/277)首次提出。
BGV最重要的贡献是提出了模数转换(Modular Switching)技术,有效地控制了同态运算带来的密文噪声增加，从而构造Leveled FHE：即这样的FHE可以实现给定计算深度的同态计算任务。但是，BGV并不能实现任意深度下的同态计算，不能算做真正意义上的FHE。

## 预备知识

### (R)LWE假设的一个变种
这里引入一个重要假设：

<p align="center">
若 <img src="https://latex.codecogs.com/svg.image?a\overset{\$}{\leftarrow}R_q,&space;s\overset{\$}{\leftarrow}\chi" title="https://latex.codecogs.com/svg.image?a\overset{\$}{\leftarrow}R_q, s\overset{\$}{\leftarrow}\chi" /> ，那么 <img src="https://latex.codecogs.com/svg.image?(a,as&plus;2e)\approx_c&space;(a,b)" title="https://latex.codecogs.com/svg.image?(a,as+2e)\approx_c (a,b)" /> 。 这里 <img src="https://latex.codecogs.com/svg.image?b\overset{\$}{\leftarrow}R_q" title="https://latex.codecogs.com/svg.image?b\overset{\$}{\leftarrow}R_q" /> 。
</p>

证明如下：依据RLWE假设，有 <img src="https://latex.codecogs.com/svg.image?(a,as&plus;e)\approx_c(a,b)" title="https://latex.codecogs.com/svg.image?(a,as+e)\approx_c(a,b)" /> 。又因为2和素数p互质，因此 <img src="https://latex.codecogs.com/svg.image?(2a,2as&plus;2e)=(a',a's&plus;2e)\approx_c&space;\left(Unif(R_q),&space;Unif(R_q)\right)" title="https://latex.codecogs.com/svg.image?(2a,2as+2e)=(a',a's+2e)\approx_c \left(Unif(R_q), Unif(R_q)\right)" />
  

## BGV基本构造
<p align="center">
  <img src="fig/BGV_construct.png" alt="animated"/>
</p>
下面简单论述BGV构造的正确性：
<div>关于密钥生成KeyGen部分，最重要的是保持关系式 <img src="https://latex.codecogs.com/svg.image?\mathbf{A}\cdot&space;\mathbf{s}&plus;2\mathbf{e}=\mathbf{b}" title="https://latex.codecogs.com/svg.image?\mathbf{A}\cdot \mathbf{s}+2\mathbf{e}=\mathbf{b}" />, 这样就可以保持pk和均匀分布不可区分。</div>

<div>关于加密Encrypt部分， 依据Left-Over Hash Lemma (LHL)可知 <img src="https://latex.codecogs.com/svg.image?\mathbf{A}^T\cdot&space;\mathbf{r}\approx_s&space;\text{Uniform}" title="https://latex.codecogs.com/svg.image?\mathbf{A}^T\cdot \mathbf{r}\approx_s \text{Uniform}" />， 最终可得 <img src="https://latex.codecogs.com/svg.image?\mathbf{c}\approx_c&space;\text{Uniform}" title="https://latex.codecogs.com/svg.image?\mathbf{c}\approx_c \text{Uniform}" /> 。</div>

<div>关于解密Decrypt部分，它的正确性可用 <img src="https://latex.codecogs.com/svg.image?\left<&space;\mathbf{c},\mathbf{s}\right>=\left<&space;\mathbf{m}&plus;\mathbf{A}^T\mathbf{r},&space;\mathbf{s}\right>=m&plus;2\mathbf{r}^T\cdot&space;\mathbf{e}\equiv&space;m&space;(\bmod&space;2)" /> 表示。</div>

<!---title="https://latex.codecogs.com/svg.image?\left< \mathbf{c},\mathbf{s}\right>=\left< \mathbf{m}+\mathbf{A}^T\mathbf{r}, \mathbf{s}\right>=m+2\mathbf{r}^T\cdot \mathbf{e}\equiv m (\bmod 2)" -->

## BGV同态运算
BGV可以支持的运算包括加（减）法，乘法。乘法运算中的密文噪声增长很快，因此限制了BGV的运算深度。

### 同态加法
同态加法很容易做到，只需要将BGV密文视为向量，然后做相应的向量加法即可。也就是说，

<p align="center">
<img src="https://latex.codecogs.com/svg.image?\mathbf{c_0}&plus;\mathbf{c_1}=\mathbf{m_0}&plus;\mathbf{A}^T\mathbf{r_0}&plus;\mathbf{m_1}&plus;\mathbf{A}^T\mathbf{r_1}=(\mathbf{m_0}&plus;\mathbf{m_1})&plus;\mathbf{A}^T(\mathbf{r_0}&plus;\mathbf{r_1})" title="https://latex.codecogs.com/svg.image?\mathbf{c_0}+\mathbf{c_1}=\mathbf{m_0}+\mathbf{A}^T\mathbf{r_0}+\mathbf{m_1}+\mathbf{A}^T\mathbf{r_1}=(\mathbf{m_0}+\mathbf{m_1})+\mathbf{A}^T(\mathbf{r_0}+\mathbf{r_1})" />
</p>

### 同态乘法
同态乘法就要困难多了。BGV的思路是对密文向量做tensor product。

## BGV Bootstrapping
Bootstrapping使得BGV从leveled FHE变换成FHE。它的技术细节很复杂，不在这里描述。
