# FHE噪声增长分析

## 预备知识

### 关于erf函数
误差函数(error function)是统计学中的一个重要函数。在FHE噪声分析中我们使用误差函数来估计噪声幅度在某个区间的概率。如果这个概率非常接近1，那么认定噪声只在该区间抖动变化。erf函数的定义如下:
<p align="center">
<img src="https://latex.codecogs.com/svg.image?erf(x)=\frac{1}{\sqrt{\pi}}\int_{-x}^{x}e^{-t^2}dt=\frac{2}{\sqrt{\pi}}\int_{0}^{x}e^{-t^2}dt" title="erf(x)=\frac{1}{\sqrt{\pi}}\int_{-x}^{x}e^{-t^2}dt=\frac{2}{\sqrt{\pi}}\int_{0}^{x}e^{-t^2}dt" />
 </p>
 
引入erf函数的一个重要性质，该性质沟通了erf函数和高斯分布:
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\Phi(x)=\frac{1}{2}&plus;\frac{1}{2}\text{&space;erf}(\frac{x}{\sqrt{2}})&space;" title="\Phi(x)=\frac{1}{2}+\frac{1}{2}\text{ erf}(\frac{x}{\sqrt{2}}) " />
 </p>
 
 对上式做一些简单变换，可得：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\text{&space;erf}(\frac{x}{\sqrt{2}})&space;=&space;2(\Phi(x)-\Phi(0))" title="\text{ erf}(\frac{x}{\sqrt{2}}) = 2(\Phi(x)-\Phi(0))" />
</p>
也就是说，一个服从高斯分布的随机变量出现在区间[-x,+x]的概率可以用erf函数直接计算。

### 高斯分布的叠加性

## FHE密文被正确解密的概率
我们回到FHE噪声分析的核心问题，那就是“给定FHE密文噪声分布(通常是均值为0，方差为 <img src="https://latex.codecogs.com/svg.image?\sigma^2" title="\sigma^2" /> 的高斯分布)，那么该密文可以被正确解密的概率是多少？” 注意这里正确解密的含义是 <img src="https://latex.codecogs.com/svg.image?|e|<\frac{q}{2t}" title="|e|<\frac{q}{2t}" /> 。这就表明噪声随机变量e应该以大概率出现在区间 <img src="https://latex.codecogs.com/svg.image?[-\frac{q}{2t},&space;\frac{q}{2t}]" title="[-\frac{q}{2t}, \frac{q}{2t}]" /> 。等价的表述方式是e落在区间 <img src="https://latex.codecogs.com/svg.image?[-\frac{q}{2t},&space;\frac{q}{2t}]" title="[-\frac{q}{2t}, \frac{q}{2t}]" /> 之外的概率是可忽略的(例如概率为 <img src="https://latex.codecogs.com/svg.image?2^{-30}" title="2^{-30}" /> ):
<p align="center">
<img src="https://latex.codecogs.com/svg.image?1-\text{erf}(\frac{q}{2t\sigma\cdot&space;\sqrt{2}})\leq&space;2^{-i}" title="1-\text{erf}(\frac{q}{2t\sigma\cdot \sqrt{2}})\leq 2^{-i}" /> </p>

实践表明上面的分析方法是悲观的，即实际FHEW的解密错误概率通常比上式得到的概率要小的多。因此FHEW论文提供另外一种分析方法。这里我们避开分析单个FHE密文可解密的概率，转而分析两个(独立)FHE密文的噪声之和。假定单个FHE密文 <img src="https://latex.codecogs.com/svg.image?\mathbf{c_i}" title="\mathbf{c_i}" /> 的噪声满足 <img src="https://latex.codecogs.com/svg.image?|e_i|&space;=&space;|err(\mathbf{c_i})|<q/2t" title="|e_i| = |err(\mathbf{c_i})|<q/2t" /> 。那么两个独立FHE密文的噪声之和应满足 <img src="https://latex.codecogs.com/svg.image?|e_1&plus;e_2|=|err(\mathbf{c_1})&plus;err(\mathbf{c_2})|<q/t" title="|e_1+e_2|=|err(\mathbf{c_1})+err(\mathbf{c_2})|<q/t" /> 。因此FHEW用下式来估算解密的出错概率：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?1-\text{erf}(\frac{q}{t\sigma\cdot&space;\sqrt{2}})\leq&space;2^{-i}" title="1-\text{erf}(\frac{q}{t\sigma\cdot \sqrt{2}})\leq 2^{-i}" /> </p>

## 密钥变换/模数变换对FHEW密文噪声的影响
这里分析一下密钥变换和模数变换的正确性和噪声增长。

### 模数变换
给定一个LWE密文 <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{Q/t}(m)=(\mathbf{a},b)" title="LWE_{\mathbf{s}}^{Q/t}(m)=(\mathbf{a},b)" /> , 定义LWE模数变换如下：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?ModSwitch(\mathbf{a},b)=(\mathbf{a'},b')=((\lfloor&space;qa_0/Q\rceil,\cdots,\lfloor&space;qa_{n-1}/Q\rceil),&space;\lfloor&space;qb/Q\rceil&space;)" title="ModSwitch(\mathbf{a},b)=(\mathbf{a'},b')=((\lfloor qa_0/Q\rceil,\cdots,\lfloor qa_{n-1}/Q\rceil), \lfloor qb/Q\rceil )" /></p>

引理: 如果输入的LWE密文(高斯)噪声 <img src="https://latex.codecogs.com/svg.image?err((\mathbf{a},b))" title="err((\mathbf{a},b))" /> 标准差为 <img src="https://latex.codecogs.com/svg.image?\sigma" title="\sigma" /> ，那么 <img src="https://latex.codecogs.com/svg.image?ModSwitch(\mathbf{a},b)" title="ModSwitch(\mathbf{a},b)" /> 输出的LWE密文(高斯)噪声标准差为 <img src="https://latex.codecogs.com/svg.image?\sqrt{(q\sigma/Q)^2&space;&plus;&space;(||\mathbf{s}||^2&plus;1)/12}" title="\sqrt{(q\sigma/Q)^2 + (||\mathbf{s}||^2+1)/12}" />, 这里
<img src="https://latex.codecogs.com/svg.image?||s||=\sqrt{\sum_{i=0}^{n-1}s_i^2}" title="||s||=\sqrt{\sum_{i=0}^{n-1}s_i^2}" />

*证明* 首先我们有 <img src="https://latex.codecogs.com/svg.image?a_i'=\frac{q}{Q}a_i&plus;r_i,&space;b'=\frac{q}{Q}b&plus;r_n" title="a_i'=\frac{q}{Q}a_i+r_i, b'=\frac{q}{Q}b+r_n" /> 。这里 <img src="https://latex.codecogs.com/svg.image?r_i&space;\sim&space;Unif(-\frac{1}{2},\frac{1}{2})" title="r_i \sim Unif(-\frac{1}{2},\frac{1}{2})" /> 。
经过计算可得 <img src="https://latex.codecogs.com/svg.image?err(\mathbf{a'},b)=b'-\mathbf{a'}\cdot&space;\mathbf{s'}-\frac{qm}{t}=\frac{qerr(\mathbf{a},b)}{Q}&plus;r_{n}-\sum_{i=0}^{n-1}s_ir_i" title="err(\mathbf{a'},b)=b'-\mathbf{a'}\cdot \mathbf{s'}-\frac{qm}{t}=\frac{qerr(\mathbf{a},b)}{Q}+r_{n}-\sum_{i=0}^{n-1}s_ir_i" /> 。依据中心极限定理和高斯分布叠加性容易得 <img src="https://latex.codecogs.com/svg.image?err(\mathbf{a'},b)\sim&space;\mathcal{N}(0,(\frac{q\sigma}{Q})^2&plus;\frac{||\mathbf{s}||^2&plus;1}{12})" title="err(\mathbf{a'},b)\sim \mathcal{N}(0,(\frac{q\sigma}{Q})^2+\frac{||\mathbf{s}||^2+1}{12})" /> 。

### 密钥变换
#### 首先讨论FHEW论文中的形式
给定一个LWE密文 <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{z}}^{q/t}(m)=(\mathbf{a},b)" title="LWE_{\mathbf{z}}^{q/t}(m)=(\mathbf{a},b)" /> ，定义Key-Switching Key 
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\mathbf{k}_{i,j,v}=LWE_{\mathbf{s}}^{q/q}(vz_iB_{ks}^j),i=0,\cdots,N-1,&space;j=0,\cdots,d_{ks}-1,&space;v\in&space;\{0,\cdots,B_{ks}\},&space;d_{ks}=\lceil&space;log_{B_{ks}}q\rceil" title="\mathbf{k}_{i,j,v}=LWE_{\mathbf{s}}^{q/q}(vz_iB_{ks}^j),i=0,\cdots,N-1, j=0,\cdots,d_{ks}-1, v\in \{0,\cdots,B_{ks}\}, d_{ks}=\lceil log_{B_{ks}}q\rceil" /> 
 </p>
对向量a中的每一个元素施加扁平化操作 
<p align="center">
 <img src="https://latex.codecogs.com/svg.image?a_i=\sum_j&space;a_{i,j}B_{ks}^j" title="a_i=\sum_j a_{i,j}B_{ks}^j" /> 
 </p>
 那么KeySwitch操作定义如下：
<p align="center">
 <img src="https://latex.codecogs.com/svg.image?KeySwitch((\mathbf{a},b),\{\mathbf{k}_{i,j,v}\})=(\mathbf{0},b)-\sum_{i,j}\mathbf{k}_{i,j,a_{i,j}}"  title="KeySwitch((\mathbf{a},b),\{\mathbf{k}_{i,j,v}\})=(\mathbf{0},b)-\sum_{i,j}\mathbf{k}_{i,j,a_{i,j}}" />
</p>

引理：假定LWE的输入密文噪声服从 <img src="https://latex.codecogs.com/svg.image?err(LWE_{\mathbf{z}}^{q/t}(m))\sim&space;\mathcal{N}(0,\alpha^2)" title="err(LWE_{\mathbf{z}}^{q/t}(m))\sim \mathcal{N}(0,\alpha^2)" />， Key-Switching Key的噪声服从 <img src="https://latex.codecogs.com/svg.image?err(LWE_{\mathbf{s}}^{q/q}(vz_iB_{ks}^j))=err(\mathbf{k}_{i,j,v})\sim&space;\mathcal{N}(0,\sigma^2)" title="err(LWE_{\mathbf{s}}^{q/q}(vz_iB_{ks}^j))=err(\mathbf{k}_{i,j,v})\sim \mathcal{N}(0,\sigma^2)" /> ，那么KeySwitch输出的LWE密文噪声服从 <img src="https://latex.codecogs.com/svg.image?err(LWE_{\mathbf{s}}^{q/t}(m))\sim&space;\mathcal{N}(0,\alpha^2&plus;Nd_{ks}\sigma^2)" title="err(LWE_{\mathbf{s}}^{q/t}(m))\sim \mathcal{N}(0,\alpha^2+Nd_{ks}\sigma^2)" /> 。

*证明*  令 <img src="https://latex.codecogs.com/svg.image?e_{i,j,k}=err(\mathbf{k}_{i,j,v})," title="e_{i,j,k}=err(\mathbf{k}_{i,j,v})," /> ，显然存在 <img src="https://latex.codecogs.com/svg.image?\mathbf{a'}_{i,j,v}\in&space;\mathbb{Z}_q^n" title="\mathbf{a'}_{i,j,v}\in \mathbb{Z}_q^n" /> ，使得 <img src="https://latex.codecogs.com/svg.image?\mathbf{k}_{i,j,v}=(\mathbf{a'}_{i,j,v},&space;\mathbf{a'}_{i,j,v}\cdot&space;\mathbf{s}&plus;vz_iB_{ks}^j&plus;e_{i,j,v})" title="\mathbf{k}_{i,j,v}=(\mathbf{a'}_{i,j,v}, \mathbf{a'}_{i,j,v}\cdot \mathbf{s}+vz_iB_{ks}^j+e_{i,j,v})" /> 。密钥变换的输出密文 <img src="https://latex.codecogs.com/svg.image?KeySwitch(\mathbf{a},b)=(\mathbf{a'},b')" title="KeySwitch(\mathbf{a},b)=(\mathbf{a'},b')" /> 满足：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\mathbf{a'}&space;=&space;-\sum_{i,j}\mathbf{a'}_{i,j,a_{ij}}" title="\mathbf{a'} = -\sum_{i,j}\mathbf{a'}_{i,j,a_{ij}}" />
</p>
<p align="center">
<img src="https://latex.codecogs.com/svg.image?b'=b-\sum_{i,j}(\mathbf{a'}_{i,j,a_{i,j}}\cdot&space;\mathbf{s}&plus;a_{i,j}z_iB_{ks}^j&plus;e_{i,j,a_{i,j}})=b-\mathbf{a}\cdot\mathbf{z}&plus;\mathbf{a'}\cdot\mathbf{s}-\sum_{i,j}e_{i,j,a_{i,j}}" title="b'=b-\sum_{i,j}(\mathbf{a'}_{i,j,a_{i,j}}\cdot \mathbf{s}+a_{i,j}z_iB_{ks}^j+e_{i,j,a_{i,j}})=b-\mathbf{a}\cdot\mathbf{z}+\mathbf{a'}\cdot\mathbf{s}-\sum_{i,j}e_{i,j,a_{i,j}}" />
 </p>
因此，
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\begin{align*}err(\mathbf{a'},b')&=&space;b'-\mathbf{a'}\cdot\mathbf{s}-\frac{qm}{t}\\&=b-\mathbf{a}\cdot\mathbf{z}-\sum_{i,j}e_{i,j,a_{i,j}}-\frac{qm}{t}\\&=err(\mathbf{a,}b)-\sum_{i,j}e_{i,j,a_{i,j}}\end{align*}&space;" title="\begin{align*}err(\mathbf{a'},b')&= b'-\mathbf{a'}\cdot\mathbf{s}-\frac{qm}{t}\\&=b-\mathbf{a}\cdot\mathbf{z}-\sum_{i,j}e_{i,j,a_{i,j}}-\frac{qm}{t}\\&=err(\mathbf{a,}b)-\sum_{i,j}e_{i,j,a_{i,j}}\end{align*} " />
 </p>
<div>依据高斯分布叠加性最终得 <img src="https://latex.codecogs.com/svg.image?err(\mathbf{a'},b')\sim&space;\mathcal{N}(0,\alpha^2&plus;Nd_{ks}\sigma^2)" title="err(\mathbf{a'},b')\sim \mathcal{N}(0,\alpha^2+Nd_{ks}\sigma^2)" /></div>

#### 接着讨论TFHE论文中的形式

## Blind Rotation 对FHEW/TFHE密文噪声的影响
在讨论Blind Rotation之前，我们先讨论Cmux，令 <img src="https://latex.codecogs.com/svg.image?&space;\mathbf{C_{2d_g\times&space;2}}=RGSW(C),&space;&space;&space;\mathbf{d_i}=RLWE(m_i),&space;C\in\{0,1\},&space;m_i\in&space;R_{n,q}" title="https://latex.codecogs.com/svg.image? \mathbf{C_{2d_g\times 2}}=RGSW(C), \mathbf{d_i}=RLWE(m_i), C\in\{0,1\}, m_i\in R_{n,q}" />，定义<img src="https://latex.codecogs.com/svg.image?CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0})=RLWE(m_C)" title="https://latex.codecogs.com/svg.image?CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0})=RLWE(m_C)" /> 如下：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0})=\mathbf{C}\diamond&space;(\mathbf{d_1}-\mathbf{d_0})&plus;\mathbf{d_0}" title="https://latex.codecogs.com/svg.image?CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0})=\mathbf{C}\diamond&space;(\mathbf{d_1}-\mathbf{d_0})&plus;\mathbf{d_0}" />
 </p>
从Cmux的定义式可以看出噪声增长主要是RGSW乘法引起的。具体地，展开Cmux的定义式可得：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\small&space;Gadget\_Decompose(\mathbf{d_1}-\mathbf{d_0})\cdot&space;\mathbf{Z_C}&space;&plus;&space;(\mathbf{z_{d_0}}&plus;C(\mathbf{z_{d_1}-z_{d_0})})&plus;&space;(0,m_0&plus;C(m_1-m_0))" title="https://latex.codecogs.com/svg.image?\small Gadget\_Decompose(\mathbf{d_1}-\mathbf{d_0})\cdot \mathbf{Z_C} + (\mathbf{z_{d_0}}+C(\mathbf{z_{d_1}-z_{d_0})})+ (0,m_0+C(m_1-m_0))" />
 </p>
<div> 第一项的噪声部分为 <img src="https://latex.codecogs.com/svg.image?\begin{align*}\sum_{i=0}^{d_g-1}a_ie_i&plus;\sum_{i=0}^{d_g-1}b_ie_i'&space;\\\end{align*}&space;" title="https://latex.codecogs.com/svg.image?\begin{align*}\sum_{i=0}^{d_g-1}a_ie_i+\sum_{i=0}^{d_g-1}b_ie_i' \\\end{align*} " />，其中 <img src="https://latex.codecogs.com/svg.image?\{e_i\},\{e_i'\}\gets&space;Error(RGSW(m_1))" title="https://latex.codecogs.com/svg.image?\{e_i\},\{e_i'\}\gets Error(RGSW(m_1))" />
 
第二项的噪声部分为 <img src="https://latex.codecogs.com/svg.image?Err(\mathbf{d_0})&plus;C\cdot(Err(\mathbf{d_1})-Err(\mathbf{d_0}))=Err(\mathbf{d_C})" title="https://latex.codecogs.com/svg.image?Err(\mathbf{d_0})+C\cdot(Err(\mathbf{d_1})-Err(\mathbf{d_0}))=Err(\mathbf{d_C})" />
 
第三项的噪声部分为零。</div>
综上所述，可以得到Cmux噪声增长方差的上界为：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?Var(CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0}))\leq&space;2d_gn\frac{B_g^2}{3}\cdot&space;Var(Err(\mathbf{C}))&plus;max(Var(Err(\mathbf{d_0})),Var(Err(\mathbf{d_1})))" title="https://latex.codecogs.com/svg.image?Var(CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0}))\leq 2d_gn\frac{B_g^2}{3}\cdot Var(Err(\mathbf{C}))+max(Var(Err(\mathbf{d_0})),Var(Err(\mathbf{d_1})))" />
</p>

现在讨论blind rotation, 它是TFHE bootstrapping算法中的核心操作(也就是homomorphic accumulator的核心操作)。 单次blind rotation可以抽象的表示成:
 <p align="center">
  <img src="./fig/blind_rotate.PNG" alt="animated" />
 </p>
<div>注意到 <img src="https://latex.codecogs.com/svg.image?\textbf{acc}&plus;(X^{u\cdot&space;c}-1)(\textbf{acc}\diamond&space;\mathbf{Z}_u)=CMux(\mathbf{Z}_u,&space;X^{u\cdot&space;c}\cdot&space;\textbf{acc},&space;\textbf{acc})" title="https://latex.codecogs.com/svg.image?\textbf{acc}+(X^{u\cdot c}-1)(\textbf{acc}\diamond \mathbf{Z}_u)=CMux(\mathbf{Z}_u, X^{u\cdot c}\cdot \textbf{acc}, \textbf{acc})" /> 因此单次blind rotation就是一次Cmux操作。</div>

完整的blind rotation操作需要做|U|n次Cmux，如下图所示
 <p align="center">
  <img src="./fig/blind_rotate2.PNG" alt="animated" />
 </p>

