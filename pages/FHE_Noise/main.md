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

FHEW和TFHE的M.S.是一致的。注意这里的分析假定 <img src="https://latex.codecogs.com/svg.image?s_i\overset{\$}{\leftarrow}\{0,1,-1\}" title="https://latex.codecogs.com/svg.image?s_i\overset{\$}{\leftarrow}\{0,1,-1\}" />，<img src="https://latex.codecogs.com/svg.image?HW(\mathbf{s})=||\mathbf{s}||^2" title="https://latex.codecogs.com/svg.image?HW(\mathbf{s})=||\mathbf{s}||^2" /> 。


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
TFHE的K.S.思路和FHEW是一致的。区别在于ksK不夹带 <img src="https://latex.codecogs.com/svg.image?a_{i,j}" title="https://latex.codecogs.com/svg.image?a_{i,j}" />，即
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\mathbf{ksk}_{i,j}=LWE_{\mathbf{s}}^{q/q}(z_iB_{ks}^j),i=[N],&space;j=[d_{ks}],&space;d_{ks}=\lceil&space;log_{B_{ks}}q\rceil" title="https://latex.codecogs.com/svg.image?\mathbf{ksk}_{i,j}=LWE_{\mathbf{s}}^{q/q}(z_iB_{ks}^j),i=[N], j=[d_{ks}], d_{ks}=\lceil log_{B_{ks}}q\rceil" />
 </p>
相应地，KeySwitch操作定义修改如下：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?KeySwitch((\mathbf{a},b),\{\mathbf{ksk}_{i,j}\})=(\mathbf{0},b)-\sum_{i,j}a_{ij}\cdot\mathbf{ksk}_{i,j}" title="https://latex.codecogs.com/svg.image?KeySwitch((\mathbf{a},b),\{\mathbf{ksk}_{i,j}\})=(\mathbf{0},b)-\sum_{i,j}a_{ij}\cdot\mathbf{ksk}_{i,j}" />
</p>

同理可以分析KeySwitch的Error项为：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?Err(KeySwitch(\cdots))=Err(\mathbf{a'},b)=Err(\mathbf{a},b)&plus;\sum_{i,j}a_{ij}\cdot&space;e_{i,j}" title="https://latex.codecogs.com/svg.image?Err(KeySwitch(\cdots))=Err(\mathbf{a'},b)=Err(\mathbf{a},b)+\sum_{i,j}a_{ij}\cdot e_{i,j}" />
</p>
<div>假定 <img src="https://latex.codecogs.com/svg.image?a_{i,j}\sim&space;Unif(0,B_{ks})" title="https://latex.codecogs.com/svg.image?a_{i,j}\sim Unif(0,B_{ks})" />，借助这篇关于GSW的<a href="https://github.com/davidhoo1988/davidhu.github.io/blob/gh-pages/pages/GSW/main.md">博文</a>的分析方法，可以推出KeySwitch的噪声上限为：</div>
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\sigma_{KS}^2\leq&space;\alpha^2&plus;N\cdot&space;d_{ks}\cdot&space;\frac{B_{ks}^2}{3}\cdot&space;\sigma^2" title="https://latex.codecogs.com/svg.image?\sigma_{KS}^2\leq \alpha^2+N\cdot d_{ks}\cdot \frac{B_{ks}^2}{3}\cdot \sigma^2" />
</p>
和FHEW K.S.相比，TFHE K.S.的 ksk更小，但是噪声控制不如FHEW。


最后讨论TFHE中的另外一种K.S.变种，即将LWE密文切换成RLWE密文，即:
<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{z}}(m)\xrightarrow[]{KeySwitch(\cdot)}&space;RLWE_s(m)" title="https://latex.codecogs.com/svg.image?LWE_{\mathbf{z}}(m)\xrightarrow[]{KeySwitch(\cdot)} RLWE_s(m)" />
</p>

<div>类似的，可以定义KSKey为<img src="https://latex.codecogs.com/svg.image?ksk_{i,j}=RLWE_{s}^{q/q}(z_iB_{ks}^j)" title="https://latex.codecogs.com/svg.image?ksk_{i,j}=RLWE_{s}^{q/q}(z_iB_{ks}^j)" /> ，并对LWE密文的a向量做扁平化操作得 <img src="https://latex.codecogs.com/svg.image?a_i=\sum_ja_{ij}B_{ks}^j" title="https://latex.codecogs.com/svg.image?a_i=\sum_ja_{ij}B_{ks}^j" /> 。KeySwitch操作可定义成：</div>

<p align="center">
<img src="https://latex.codecogs.com/svg.image?KeySwitch((\mathbf{a},b),\{ksk_{i,j}\})=(0,b)-\sum_{i,j}a_{ij}\cdot&space;ksk_{i,j}" title="https://latex.codecogs.com/svg.image?KeySwitch((\mathbf{a},b),\{ksk_{i,j}\})=(0,b)-\sum_{i,j}a_{ij}\cdot ksk_{i,j}" />
</p>
它的噪声分析结果和LWE-LWE KeySwitch完全一致，不再赘述。

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
综上所述，可以得到Cmux噪声增长方差的上界公式为：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?Var(CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0}))\leq&space;2d_gn\frac{B_g^2}{3}\cdot&space;Var(Err(\mathbf{C}))&plus;max(Var(Err(\mathbf{d_0})),Var(Err(\mathbf{d_1})))" title="https://latex.codecogs.com/svg.image?Var(CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0}))\leq 2d_gn\frac{B_g^2}{3}\cdot Var(Err(\mathbf{C}))+max(Var(Err(\mathbf{d_0})),Var(Err(\mathbf{d_1})))" />
</p>
<div>换句话说，Cmux的输入噪声为 <img src="https://latex.codecogs.com/svg.image?max(Var(Err(\mathbf{d_0})),Var(Err(\mathbf{d_1})))" title="https://latex.codecogs.com/svg.image?max(Var(Err(\mathbf{d_0})),Var(Err(\mathbf{d_1})))" />， 输入输出噪声差为 <img src="https://latex.codecogs.com/svg.image?2d_gn\frac{B_g^2}{3}\cdot&space;Var(Err(\mathbf{C}))" title="https://latex.codecogs.com/svg.image?2d_gn\frac{B_g^2}{3}\cdot Var(Err(\mathbf{C}))" /> 。</div>

现在讨论blind rotation, 它是TFHE bootstrapping算法中的核心操作(也就是homomorphic accumulator的核心操作)。 单次blind rotation可以抽象的表示成:
 <p align="center">
  <img src="./fig/blind_rotate.PNG" alt="animated" />
 </p>
<div>注意到 <img src="https://latex.codecogs.com/svg.image?\textbf{acc}&plus;(X^{u\cdot&space;c}-1)(\textbf{acc}\diamond&space;\mathbf{Z}_u)=CMux(\mathbf{Z}_u,&space;X^{u\cdot&space;c}\cdot&space;\textbf{acc},&space;\textbf{acc})" title="https://latex.codecogs.com/svg.image?\textbf{acc}+(X^{u\cdot c}-1)(\textbf{acc}\diamond \mathbf{Z}_u)=CMux(\mathbf{Z}_u, X^{u\cdot c}\cdot \textbf{acc}, \textbf{acc})" /> 因此单次blind rotation就是一次Cmux操作。</div>


举例说明当 <img src="https://latex.codecogs.com/svg.image?s_i\in\{0,1,-1\}" title="https://latex.codecogs.com/svg.image?s_i\in\{0,1,-1\}" />，如何计算 <img src="https://latex.codecogs.com/svg.image?\mathbf{acc}\cdot&space;X^{-a_is_i}&space;" title="https://latex.codecogs.com/svg.image?\mathbf{acc}\cdot X^{-a_is_i} " /> 。
将+1和-1视为‘基底’，那么 <img src="https://latex.codecogs.com/svg.image?s_i=&space;1\cdot&space;s_{i,1}&plus;(-1)\cdot&space;s_{i,-1}" title="https://latex.codecogs.com/svg.image?s_i= 1\cdot s_{i,1}+(-1)\cdot s_{i,-1}" />，这里 <img src="https://latex.codecogs.com/svg.image?s_{i,1}\in\{0,1\},s_{i,-1}\in\{0,1\}" title="https://latex.codecogs.com/svg.image?s_{i,1}\in\{0,1\},s_{i,-1}\in\{0,1\}" /> 。因此需要做两次Cmux运算得到 <img src="https://latex.codecogs.com/svg.image?\mathbf{acc}\cdot&space;X^{-a_is_i}&space;" title="https://latex.codecogs.com/svg.image?\mathbf{acc}\cdot X^{-a_is_i} " /> ：先做一次 <img src="https://latex.codecogs.com/svg.image?Cmux(RGSW(s_{i,1}),&space;\mathbf{acc}\cdot&space;X^{-a_i},&space;\mathbf{acc})" title="https://latex.codecogs.com/svg.image?Cmux(RGSW(s_{i,1}), \mathbf{acc}\cdot X^{-a_i}, \mathbf{acc})" />，再做一次 <img src="https://latex.codecogs.com/svg.image?Cmux(RGSW(s_{i,-1}),&space;\mathbf{acc}\cdot&space;X^{a_i},&space;\mathbf{acc})" title="https://latex.codecogs.com/svg.image?Cmux(RGSW(s_{i,-1}), \mathbf{acc}\cdot X^{a_i}, \mathbf{acc})" /> 。

完整的blind rotation操作需要做|U|n次Cmux，如下图所示
 <p align="center">
  <img src="./fig/blind_rotate2.PNG" alt="animated" />
 </p>
 <div>因此整个blind rotation的噪声增加量为</div>
  <p align="center">
 <img src="https://latex.codecogs.com/svg.image?BlindRotate(\cdots)\leq&space;Var(Err(\mathbf{acc}))&plus;|U|\cdot&space;p\cdot&space;\left(2d_gn\frac{B_g^2}{3}\cdot&space;Var(Err(\mathbf{Z_u}))\right)" title="https://latex.codecogs.com/svg.image?BlindRotate(\cdots)\leq Var(Err(\mathbf{acc}))+|U|\cdot p\cdot \left(2d_gn\frac{B_g^2}{3}\cdot Var(Err(\mathbf{Z_u}))\right)" />
  </p>

## Bootstrapping的噪声分析
最后，我们试着分析 Bootstrapping的噪声增长。Boostrapping的算法流程如下图所示：
 <p align="center">
  <img src="./fig/bootstrap.PNG" alt="animated" />
 </p>
 
容易知道Bootstrap对噪声有影响的操作是KeySwitch, ModSwitch, BlindRotate。其中KeySwitch和BlindRotate增加噪声，ModSwitch减少噪声，用公式表示，总噪声记为
 <p align="center">
   <img src="https://latex.codecogs.com/svg.image?\sigma_{Bootstrap}^2\leq&space;\frac{Q^2}{q^2}\left(\sigma_{BR}^2&plus;\sigma_{KS}^2\right)&plus;\frac{||\mathbf{s}||^2&plus;1}{12}" title="https://latex.codecogs.com/svg.image?\sigma_{Bootstrap}^2\leq \frac{Q^2}{q^2}\left(\sigma_{BR}^2+\sigma_{KS}^2\right)+\frac{||\mathbf{s}||^2+1}{12}" />
 </p>

这里有 <img src="https://latex.codecogs.com/svg.image?acc_{BR,f}\sim&space;\mathcal{N}(0,\sigma_{BR}^2)&space;\text{&space;with&space;}&space;\sigma_{BR}^2\leq&space;N\cdot&space;2nd_gB_g^2\cdot&space;\sigma_{brK}^2&space;" title="https://latex.codecogs.com/svg.image?acc_{BR,f}\sim \mathcal{N}(0,\sigma_{BR}^2) \text{ with } \sigma_{BR}^2\leq N\cdot 2nd_gB_g^2\cdot \sigma_{brK}^2 " /> 且

<img src="https://latex.codecogs.com/svg.image?ct_{Q,ksk}\sim&space;\mathcal{N}(0,\sigma_{ct}^2)&space;\text{&space;with&space;}&space;\sigma_{ct}^2\leq&space;\sigma_{BR}^2&plus;Nd_ks\frac{B_{ks}^2}{3}\cdot&space;\sigma^2_{ksk}=\sigma_{BR}^2&plus;\sigma_{KS}^2" title="https://latex.codecogs.com/svg.image?ct_{Q,ksk}\sim \mathcal{N}(0,\sigma_{ct}^2) \text{ with } \sigma_{ct}^2\leq \sigma_{BR}^2+Nd_ks\frac{B_{ks}^2}{3}\cdot \sigma^2_{ksk}=\sigma_{BR}^2+\sigma_{KS}^2" /> 。
