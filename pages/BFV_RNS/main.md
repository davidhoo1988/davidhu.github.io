# RNS版本的BFV方案

这篇博文主要基于20116年的[BEHZ论文](https://eprint.iacr.org/2016/510)。目前所有实用的第二代FHE方案都是在RNS(Residue Number System, or Chinese Remainder Theorem Representation)上做的。这是因为RNS具有很高的计算并行度，并且可以利用计算机自带的整数类型(int32, int64)直接支持FHE方案需要的大数
（$\mathbf{F}_{p}, \text{p is at least hundreds of bits long}$)

在正常数制下的BFV的介绍可以参考我之前的[一篇博文](https://github.com/davidhoo1988/davidhu.github.io/edit/gh-pages/pages/BFV/main.md)。


## 预备知识
### 模运算 Modular Arithemtic
模运算是定义在整数上的一类运算，定义如下
$$a\bmod p = |a|_p=a-\lfloor \frac{a}{p}\rfloor \cdot p$$

注意$|a|_p\in [0,\cdots,p-1]$， 在LWE/FHE里，还会采用中心化的模运算定义如下
 <p align="center">
<img src="https://latex.codecogs.com/svg.image?[a]_p=\begin{cases}&space;&&space;|a|_p\text{&space;if&space;}&space;|a|_p\leq\lfloor\frac{p-1}{2}\rfloor&space;\\&space;&&space;|a|_p&space;-&space;p&space;\text{&space;otherwise&space;}&space;&space;\end{cases}" title="https://latex.codecogs.com/svg.image?[a]_p=\begin{cases} & |a|_p\text{ if } |a|_p\leq\lfloor\frac{p-1}{2}\rfloor \\ & |a|_p - p \text{ otherwise } \end{cases}" />
</p>

注意中心化的模运算的计算范围是 $[a]_p \in [-\lfloor\frac{p}{2}\rfloor , \lfloor\frac{p-1}{2}\rfloor]$

### 中国剩余定理 Chinese Remainder Theorem

中国剩余定理CRT阐述了一个整数环的自同构的存在，即 <img src="https://latex.codecogs.com/svg.image?\mathbb{Z}_q&space;&space;\simeq&space;\prod_{i=1}^k\mathbb{Z}_{q_i}" title="https://latex.codecogs.com/svg.image?\mathbb{Z}_q \simeq \prod_{i=1}^k\mathbb{Z}_{q_i}" />。 CRT蕴含着一个非进位的数制系统(non-positional number system)，现在一般称之为余数系统(Residue Number System, RNS)。在RNS下，一个大整数(mod q)的模运算可以被拆分成k个小整数的模运算,即 <img src="https://latex.codecogs.com/svg.image?a\bmod&space;q&space;\simeq&space;(|a|_{q_1},\cdots,|a|_{q_k})" title="https://latex.codecogs.com/svg.image?a\bmod q \simeq (|a|_{q_1},\cdots,|a|_{q_k})" /> 。通常每个小整数的模运算可以被绝大多数编程语言直接支持，因此大整数的模运算也可以被支持。RNS表示的数 <img src="https://latex.codecogs.com/svg.image?(a_1,\cdots,a_k)" title="https://latex.codecogs.com/svg.image?(a_1,\cdots,a_k)" /> 可借由中国剩余定理恢复成原始大数a，即

<p align="center">
<img src="https://latex.codecogs.com/svg.image?a=&space;\sum_{i=1}^k|a_i\cdot&space;\frac{q_i}{q}|_{q_i}\cdot&space;\frac{q}{q_i}&space;\bmod&space;q" title="https://latex.codecogs.com/svg.image?a= \sum_{i=1}^k|a_i\cdot \frac{q_i}{q}|_{q_i}\cdot \frac{q}{q_i} \bmod q" />
</p>

自然地，中国剩余定理可以推广到多项式环 <img src="https://latex.codecogs.com/svg.image?R_q&space;&space;\simeq&space;\prod_{i=1}^k&space;R_{q_i}" title="https://latex.codecogs.com/svg.image?R_q \simeq \prod_{i=1}^k R_{q_i}" />

### RNS基转换
这里引入若干RNS下的计算工具，它们和BFV方案的基本操作密切相关。

首先引入快速基转换的概念。一个大整数在不同基下的RNS表示显然是不同的。现在需要从某组基q(这里基q指的是构成的一组基 <img src="https://latex.codecogs.com/svg.image?\{q_i\}_{i=1,\cdots,k}" title="https://latex.codecogs.com/svg.image?\{q_i\}_{i=1,\cdots,k}" /> ) 转换成另外一组基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}=\{m_i\}_{i=1,\cdots,\ell}" title="https://latex.codecogs.com/svg.image?\mathcal{B}=\{m_i\}_{i=1,\cdots,\ell}" />。定义快速基转换如下:


<p align="center">
  <img src="fig/RNS_fastBconv.PNG" alt="animated"/>
</p>


注意这里的“快速”指的是不需要做mod q的操作（思考一下可知如果在FastBconv中使用mod q，其实就是中国剩余定理的形式）。作为代价，FastBconv算出来的是$x+\alpha_xq, \alpha_x\in[0,k-1]$ 而不是$x$


### BFV方案
简单回顾BFV方案如下:

 <p align="center">
  <img src="fig/BFV_basic.PNG" alt="animated"/>
</p>

注意这里的公钥 $pk=(\mathbf{p}_0, \mathbf{p}_1)=([-(\mathbf{as}+\mathbf{e})]_q, \mathbf{a})$, 且 
$\mathbf{p}_0+\mathbf{p}_1\mathbf{s}=-\mathbf{e}\approx 0$ 。

据此，容易证明 $[ct(\mathbf{s})]_q = [ct[0]+ct[1]\mathbf{s}]_q = [\Delta [\mathbf{m}]_t + \mathbf{v}]_q$。 这里 $\mathbf{v}$ 是密文 $ct$ 的内置噪声。
更详尽的介绍可以参考[这篇博文](https://github.com/davidhoo1988/davidhu.github.io/edit/gh-pages/pages/BFV/main.md)

## RNS版本下的 BFV-Decryption
简单回顾BFV的解密算法如下:
 <p align="center">
  <img src="fig/BFV_decrypt.PNG" alt="animated"/>
</p>

这里引入新的记号DR来表示‘division & rounding’的操作:
 <p align="center">
  <img src="fig/BFV_dr.PNG" alt="animated"/>
</p>

所以，这里的难度主要是如何在RNS下做DR操作。下面分三个小节详细介绍这部分

### 近似求解RNS rounding
在很多计算应用中，需要处理rounding操作，然而RNS并不支持rounding。 因此只能转而先求flooring，然后用flooring的结果近似rounding，即 
<p align="center">
  <img src="fig/RNS_floor.PNG" alt="animated"/>
</p>

首先观察到 $\lfloor \frac{t}{q}[ct(\mathbf{s})]_q\rceil = \lfloor \frac{t}{q}|ct(\mathbf{s})|_q\rceil$, 因此有
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\left[\lfloor\frac{t}{q}[ct(\mathbf{s})]_q\rfloor\right&space;]_t&space;=&space;-|q^{-1}|_t\cdot&space;|t\cdot&space;ct(\mathbf{s})|_q&space;\bmod&space;t" title="https://latex.codecogs.com/svg.image?\left[\lfloor\frac{t}{q}[ct(\mathbf{s})]_q\rfloor\right ]_t = -|q^{-1}|_t\cdot |t\cdot ct(\mathbf{s})|_q \bmod t" />
</p>

这里不加证明的引入引理1，通过FastBconv近似计算$\left\lfloor \gamma\frac{t}{q}[ct(\mathbf{s})]_q\right \rceil$
<p align="center">
  <img src="fig/BFV_lemma1.PNG" alt="animated"/>
</p>

对引理1做一些重要诠释。首先明确引理1实际上算的是$\left\lfloor \gamma\frac{t}{q}[ct(\mathbf{s})]_q\right \rceil$的近似值 $\left\lfloor \gamma\frac{t}{q}[ct(\mathbf{s})]_q\right \rceil - \mathbf{e}$, 这是由于FastBconv夹带的$\alpha_xq, \alpha_x\in[0,k-1]$造成的；

其次参数$\gamma$的引入是为了控制 $\left\lfloor \gamma\frac{\mathbf{v_c}}{q}\right \rceil$ 的大小，使得 
$\left\lfloor \gamma\frac{\mathbf{v_c}}{q}\right \rceil -\mathbf{e}\in [-\lfloor\frac{\gamma}{2}\rfloor , \lfloor\frac{\gamma-1}{2}\rfloor]$ 。方便下一小节引入修正方法最终去除掉 $\left\lfloor \gamma\frac{\mathbf{v_c}}{q}\right \rceil -\mathbf{e}$

### 修正RNS rounding近似算法中的误差
对引理1的结果直接做一次中心化模$\gamma$的操作就能直接得到 $\left\lfloor \gamma\frac{\mathbf{v_c}}{q}\right \rceil -\mathbf{e}$， 
消去这部分误差就可以得到精确地division&rounding的结果即 $\gamma([\mathbf{m}]_t+t\mathbf{r})$ 。

中心化模$\gamma$操作的正确性来源于引理2：
<p align="center">
  <img src="fig/BFV_lemma2.PNG" alt="animated"/>
</p>

<div>也就是说，如果取合适大小的 <img src="https://latex.codecogs.com/svg.image?\gamma" title="https://latex.codecogs.com/svg.image?\gamma" /> 使得 <img src="https://latex.codecogs.com/svg.image?\gamma&space;\sim&space;k(\frac{1}{2}-\frac{||\mathbf{v_c}||_{\infty}}{q})^{-1}" title="https://latex.codecogs.com/svg.image?\gamma \sim k(\frac{1}{2}-\frac{||\mathbf{v_c}||_{\infty}}{q})^{-1}" /> , 那么消去误差是可能的。</div>


### RNS版本下的BFV-Decryption小结
至此，我们在算法1中描述完整的RNS版本的BFV-decryption方法:

<p align="center">
  <img src="fig/BFV_rns_decrypt.PNG" alt="animated"/>
</p>

对算法1做一些解释。第1-3行代码算出 $\mathbf{s}^{(t)}=\gamma[\mathbf{m}]_t+\lfloor\gamma\frac{\mathbf{v_c}}{q}\rceil -\mathbf{e} \bmod t, \mathbf{s}^{(\gamma)}= \lfloor\gamma\frac{\mathbf{v_c}}{q}\rceil -\mathbf{e}\bmod \gamma$ 。 

第4行代码算出误差项 $[\mathbf{s}^{(\gamma)}]_{\gamma}= \lfloor\gamma\frac{\mathbf{v_c}}{q}\rceil -\mathbf{e}$ 。 

第五行最终消去误差恢复出 $[\mathbf{m}]_t$ 。

最后，注意算法1正确返回 <img src="https://latex.codecogs.com/svg.image?[\mathbf{m}]_t" title="https://latex.codecogs.com/svg.image?[\mathbf{m}]_t" /> 的前提是 <img src="https://latex.codecogs.com/svg.image?\gamma,&space;||\mathbf{v}||_{\infty}" title="https://latex.codecogs.com/svg.image?\gamma, ||\mathbf{v}||_{\infty}" /> 满足一些bound:

<p align="center">
  <img src="fig/BFV_rns_decrypt_bound.PNG" alt="animated"/>
</p>


## RNS版本下的 BFV-Multiplication
现在讨论一个比 RNS BFV-Decryption更困难的问题，即在RNS下完成BFV-Multiplication的问题。

### 回顾 BFV-Multiplication算法
首先回顾bit_decompose相关操作：

<p align="center">
  <img src="fig/BFV_bitdecompose.PNG" alt="animated"/>
</p>

<div>注意这里取的基为 <img src="https://latex.codecogs.com/svg.image?\omega" title="https://latex.codecogs.com/svg.image?\omega" /> 且 <img src="https://latex.codecogs.com/svg.image?\mathcal{D}_{\omega,q}(\mathbf{a})=&space;(\mathbf{a}_0,\cdots,\mathbf{a}_{k-1})~s.t.~\sum_i\mathbf{a}_i{\omega}^i=\mathbf{a}\bmod&space;q" title="https://latex.codecogs.com/svg.image?\mathcal{D}_{\omega,q}(\mathbf{a})= (\mathbf{a}_0,\cdots,\mathbf{a}_{k-1})~s.t.~\sum_i\mathbf{a}_i{\omega}^i=\mathbf{a}\bmod q" /> </div>

<div>容易证明 <img src="https://latex.codecogs.com/svg.image?\left<\mathcal{D}_{\omega,q}(\mathbf{a}),&space;\mathcal{P}_{\omega,q}(\mathbf{s})\right>\equiv&space;\mathbf{a}\cdot\mathbf{s}&space;\bmod&space;q" title="https://latex.codecogs.com/svg.image?\left<\mathcal{D}_{\omega,q}(\mathbf{a}), \mathcal{P}_{\omega,q}(\mathbf{s})\right>\equiv \mathbf{a}\cdot\mathbf{s} \bmod q" /> 成立。</div>

利用bit_decompose可以描述BFV乘法算法如下：
<p align="center">
  <img src="fig/BFV_mul.PNG" alt="animated"/>
</p>

BFV乘法分两步进行。第一步做正常的多项式乘法，接着做DR操作； 第二步做重线性化re-linearization。注意relinearization key其实是一串key而不是单个key,即 <img src="https://latex.codecogs.com/svg.image?rlk_{FV}=\{RLWE_{\mathbf{s}}(\mathbf{a}\cdot\omega^i)\}_{i=0,\cdots,k-1}" title="https://latex.codecogs.com/svg.image?rlk_{FV}=\{RLWE_{\mathbf{s}}(\mathbf{a}\cdot\omega^i)\}_{i=0,\cdots,k-1}" /> 。因此 <img src="https://latex.codecogs.com/svg.image?Relin_{FV}(\cdot)" title="https://latex.codecogs.com/svg.image?Relin_{FV}(\cdot)" /> 算的实际上是 <img src="https://latex.codecogs.com/svg.image?(\mathbf{c}_0,\mathbf{c}_1)&plus;\sum_j&space;\mathbf{c}_{2,j}\cdot(rlk_{FV}[0],&space;rlk_{FV}[1])" title="https://latex.codecogs.com/svg.image?(\mathbf{c}_0,\mathbf{c}_1)+\sum_j \mathbf{c}_{2,j}\cdot(rlk_{FV}[0], rlk_{FV}[1])" /> 。

现在考虑如何将原始的BFV乘法改装成RNS形式。这里主要有两个难点。第一，算法第一步的Division&Rounding与RNS天然不兼容； 第二，算法第二步的decompose使用了除法和想下取整，同样和RNS不兼容。

### 修改第一步 division & rounding
RNS下不能直接做 division & rounding, 但可以直接做 division & flooring 。因此我们的基本思路是用flooring近似rounding，误差部分可以看作RLWE噪声分量的一部分。即
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\lfloor\frac{t}{q}ct_{\star}[j]\rceil\approx&space;\lfloor\frac{t}{q}ct_{\star}[j]\rfloor=&space;\frac{t\cdot&space;ct_{\star}[j]-|t\cdot&space;ct_{\star}[j]|_q}{q}" title="https://latex.codecogs.com/svg.image?\lfloor\frac{t}{q}ct_{\star}[j]\rceil\approx \lfloor\frac{t}{q}ct_{\star}[j]\rfloor= \frac{t\cdot ct_{\star}[j]-|t\cdot ct_{\star}[j]|_q}{q}" />
</p>

#### RNS flooring
现在考虑如何在RNS数制下表示上式。现在假定 <img src="https://latex.codecogs.com/svg.image?{t}\cdot&space;ct_{\star}[j]&space;<&space;\prod_{i=1}^{\ell}m_i" title="https://latex.codecogs.com/svg.image?{t}\cdot ct_{\star}[j] < \prod_{i=1}^{\ell}m_i" /> , 那么我们可以在基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}=\{m_i\}_{i=1,\cdots,\ell}\cup&space;m_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}=\{m_i\}_{i=1,\cdots,\ell}\cup m_{sk}" /> 表示 <img src="https://latex.codecogs.com/svg.image?{t}\cdot&space;ct_{\star}[j]" title="https://latex.codecogs.com/svg.image?{t}\cdot ct_{\star}[j]" /> 。显然 <img src="https://latex.codecogs.com/svg.image?|{t}\cdot&space;ct_{\star}[j]|_q" title="https://latex.codecogs.com/svg.image?|{t}\cdot ct_{\star}[j]|_q" />可以直接在基 <img src="https://latex.codecogs.com/svg.image?q=\{q_i\}_{i=1,\cdots,k}" title="https://latex.codecogs.com/svg.image?q=\{q_i\}_{i=1,\cdots,k}" /> 。换句话说，如果有 <img src="https://latex.codecogs.com/svg.image?{t}\cdot&space;ct_{\star}[j]" title="https://latex.codecogs.com/svg.image?{t}\cdot ct_{\star}[j]" /> 在基 <img src="https://latex.codecogs.com/svg.image?q" title="https://latex.codecogs.com/svg.image?q" /> 和 基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" /> 下的表示，就能做division & rounding。

具体地，定义RNS flooring如下：
<p align="center">
  <img src="fig/RNS_floor.PNG" alt="animated"/>
</p>

我们不加证明地使用引理5，即RNS flooring确实可以用来近似 division & rounding
<p align="center">
  <img src="fig/Lemma 5.PNG" alt="animated"/>
</p>
 
但是RNS flooring存在两个小问题。 第一，事实上我们只知道基q下的 <img src="https://latex.codecogs.com/svg.image?{t}\cdot&space;ct_{\star}[j]" title="https://latex.codecogs.com/svg.image?{t}\cdot ct_{\star}[j]" />， 怎么才能将基q下的<img src="https://latex.codecogs.com/svg.image?{t}\cdot&space;ct_{\star}[j]" title="https://latex.codecogs.com/svg.image?{t}\cdot ct_{\star}[j]" /> 转换成相应的基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" /> 下的表示？第二，RNS flooring输出的是基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" /> 下的 <img src="https://latex.codecogs.com/svg.image?\lfloor\frac{t}{q}ct_{\star}[j]\rceil&plus;\mathbf{b}_j" title="https://latex.codecogs.com/svg.image?\lfloor\frac{t}{q}ct_{\star}[j]\rceil+\mathbf{b}_j" />，最终怎么才能把这个结果转换回基q下的表示？

#### 利用蒙哥马利模约减完成基转换
现在讨论第一个小问题。注意不能使用FastBconv做基转换，因为FastBconv(x)算的不是精确x, 而是带有q-溢出 (q-overflow): <img src="https://latex.codecogs.com/svg.image?FastBconv(x,\mathcal{B}_{sk})=x&plus;\alpha_xq&space;" title="https://latex.codecogs.com/svg.image?FastBconv(x,\mathcal{B}_{sk})=x+\alpha_xq " /> 。因此需要在RNS数制下引入模q操作消去q-溢出。这里采纳的是RNS蒙哥马利模约减算法：

<p align="center">
  <img src="fig/RNS_Montgomery.PNG" alt="animated"/>
</p>

蒙哥马利模约减的原理可以参考[这篇博文](https://github.com/davidhoo1988/davidhu.github.io/blob/gh-pages/pages/MontMul/main.md)。这里设定的蒙哥马利'尾巴'为 <img src="https://latex.codecogs.com/svg.image?R=\widetilde{m}" title="https://latex.codecogs.com/svg.image?R=\widetilde{m}" /> 。

同时，因为蒙哥马利算的模约减结果带有尾巴 <img src="https://latex.codecogs.com/svg.image?\widetilde{m}^{-1}" title="https://latex.codecogs.com/svg.image?\widetilde{m}^{-1}" />, 因此需要相应修改基转换公式FastBconv使得：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?FastBconv'((\mathbf{c}_m)_{m\in&space;q})=(\sum_{i=1}^{k}|\mathbf{c}_i\frac{\widetilde{m}q_i}{q}|_{q_i}\times\frac{q}{q_i}\bmod&space;m)_{m\in&space;\mathbf{B}_{sk}\cup\{\widetilde{m}\}}=(\mathbf{c''}_m)_{m\in&space;\mathbf{B}_{sk}\cup\{\widetilde{m}\}}" title="https://latex.codecogs.com/svg.image?FastBconv'((\mathbf{c}_m)_{m\in q})=(\sum_{i=1}^{k}|\mathbf{c}_i\frac{\widetilde{m}q_i}{q}|_{q_i}\times\frac{q}{q_i}\bmod m)_{m\in \mathbf{B}_{sk}\cup\{\widetilde{m}\}}=(\mathbf{c''}_m)_{m\in \mathbf{B}_{sk}\cup\{\widetilde{m}\}}" />
</p>

这样算出基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}\cup\{\widetilde{m}\}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}\cup\{\widetilde{m}\}" /> 下的蒙哥马利形式的 <img src="https://latex.codecogs.com/svg.image?\mathbf{c''}=&space;[\widetilde{m}\mathbf{c}]_q&plus;q\mathbf{u}" title="https://latex.codecogs.com/svg.image?\mathbf{c''}= [\widetilde{m}\mathbf{c}]_q+q\mathbf{u}" /> 。接着做RNS蒙哥马利模约减刚好得到 <img src="https://latex.codecogs.com/svg.image?(\mathbf{c}_{m})_{m\in&space;\mathcal{B}_{sk}}" title="https://latex.codecogs.com/svg.image?(\mathbf{c}_{m})_{m\in \mathcal{B}_{sk}}" /> ，即基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" /> 表示的
<img src="https://latex.codecogs.com/svg.image?\mathbf{c}" title="https://latex.codecogs.com/svg.image?\mathbf{c}" /> 。

#### 利用Shenoy-Kumaresan完成基转换
现在讨论第二个小问题。已知 <img src="https://latex.codecogs.com/svg.image?x=DR_2(ct'_{\star})&plus;b" title="https://latex.codecogs.com/svg.image?x=DR_2(ct'_{\star})+b" /> 在基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" /> 的表示，我们首先使用 <img src="https://latex.codecogs.com/svg.image?FastBconv(x,\mathcal{B},m_{sk})" title="https://latex.codecogs.com/svg.image?FastBconv(x,\mathcal{B},m_{sk})" /> 得到
<img src="https://latex.codecogs.com/svg.image?x&plus;\alpha_{sk,x}M~w.r.t.~M=\prod_{m\in\mathcal{B}}m&space;" title="https://latex.codecogs.com/svg.image?x+\alpha_{sk,x}M~w.r.t.~M=\prod_{m\in\mathcal{B}}m " /> 在模 <img src="https://latex.codecogs.com/svg.image?m_{sk}" title="https://latex.codecogs.com/svg.image?m_{sk}" /> 下的表示。又因为已知x在模 <img src="https://latex.codecogs.com/svg.image?m_{sk}" title="https://latex.codecogs.com/svg.image?m_{sk}" /> 下的表示(即 <img src="https://latex.codecogs.com/svg.image?x_{sk}\overset{\underset{\mathrm{def}}{}}{=}|x|_{m_{sk}}" title="https://latex.codecogs.com/svg.image?x_{sk}\overset{\underset{\mathrm{def}}{}}{=}|x|_{m_{sk}}" /> ), 且可以预计算 <img src="https://latex.codecogs.com/svg.image?|M^{-1}|_{m_{sk}}" title="https://latex.codecogs.com/svg.image?|M^{-1}|_{m_{sk}}" /> , 所以可以算得 <img src="https://latex.codecogs.com/svg.image?|\alpha_{sk,x}|_{m_{sk}}=|(x&plus;\alpha_{sk,x}M-x_{sk})\times&space;M^{-1}|_{m_{sk}}" title="https://latex.codecogs.com/svg.image?|\alpha_{sk,x}|_{m_{sk}}=|(x+\alpha_{sk,x}M-x_{sk})\times M^{-1}|_{m_{sk}}" />。更进一步，如果模 <img src="https://latex.codecogs.com/svg.image?{m_{sk}}" title="https://latex.codecogs.com/svg.image?{m_{sk}}" /> 设置的合理，有
<img src="https://latex.codecogs.com/svg.image?|\alpha_{sk,x}|_{m_{sk}}=\alpha_{sk,x}" title="https://latex.codecogs.com/svg.image?|\alpha_{sk,x}|_{m_{sk}}=\alpha_{sk,x}" />。

整理一下，整个思路可用下式概括：

<p align="center">
<img src="https://latex.codecogs.com/svg.image?x&space;\xrightarrow[]{FastBconv(x,\mathcal{B},m_{sk})}&space;|x&plus;\alpha_{sk,x}&space;M|_{m_{sk}}&space;\xrightarrow[\times&space;|M^{-1}|_{m_{sk}}]{-x_{sk}}&space;|\alpha_{sk,x}|_{m_{sk}" title="https://latex.codecogs.com/svg.image?x \xrightarrow[]{FastBconv(x,\mathcal{B},m_{sk})} |x+\alpha_{sk,x} M|_{m_{sk}} \xrightarrow[\times |M^{-1}|_{m_{sk}}]{-x_{sk}} |\alpha_{sk,x}|_{m_{sk}" />
</p>

具体地，引理6描述了FastBconvSK操作，用来完成从较大的基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" /> 到较小基q的转换功能：

<p align="center">
  <img src="fig/FastBconvSK.PNG" alt="animated"/>
</p>

最后在本小节抛出一个这样的思考题：为什么从基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" /> 到基q的转换功能不考虑使用我们解决第一个小问题(基q到基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" /> )中的思路，即借助蒙哥马利模约减呢？

### 修改第二步 bit_decompose
现在讨论另外一种借助中国剩余定理CRT的bit_decompose方法。首先定义新的bit_decompose如下

<p align="center">
<img src="https://latex.codecogs.com/svg.image?\mathbf{c}\xrightarrow[]{\xi&space;_q(\cdot)&space;(bit-decompose)}&space;(|\mathbf{c}\frac{q_1}{q}|_{q_1},\cdots,&space;|\mathbf{c}\frac{q_k}{q}|_{q_k})" title="https://latex.codecogs.com/svg.image?\mathbf{c}\xrightarrow[]{\xi _q(\cdot) (bit-decompose)} (|\mathbf{c}\frac{q_1}{q}|_{q_1},\cdots, |\mathbf{c}\frac{q_k}{q}|_{q_k})" />
</p>

接着定义power_expand如下

<p align="center">
<img src="https://latex.codecogs.com/svg.image?\mathbf{s}^2\xrightarrow[]{\mathcal{P}&space;_{RNS,q}(\cdot)&space;(power-expand)}&space;(|\mathbf{s}^2\frac{q}{q_1}|_{q},\cdots,&space;|\mathbf{s}^2\frac{q}{q_k}|_{q})" title="https://latex.codecogs.com/svg.image?\mathbf{s}^2\xrightarrow[]{\mathcal{P} _{RNS,q}(\cdot) (power-expand)} (|\mathbf{s}^2\frac{q}{q_1}|_{q},\cdots, |\mathbf{s}^2\frac{q}{q_k}|_{q})" />
</p>

有了bit_decompose和power_expand的新定义，容易证明下面的引理:

<p align="center">
  <img src="fig/BFV_rns_lemma7.PNG" alt="animated"/>
</p>

借助引理7，可以阐述CRT版本的重线性化(relinearzation)算法。首先定义relinearzation key为 <img src="https://latex.codecogs.com/svg.image?rlk_i&space;=(rlk_i[0],&space;rlk_i[1])\overset{\underset{\mathrm{def}}{}}{=}&space;RLWE_\mathbf{s}(|s^2\frac{q}{q_i}|_q)" title="https://latex.codecogs.com/svg.image?rlk_i =(rlk_i[0], rlk_i[1])\overset{\underset{\mathrm{def}}{}}{=} RLWE_\mathbf{s}(|s^2\frac{q}{q_i}|_q)" />
 
回忆relinearization的作用是将输入 <img src="https://latex.codecogs.com/svg.image?\widetilde{ct}_{mult}&plus;b=&space;(\overline{c_0},&space;\overline{c_1},&space;\overline{c_2})" title="https://latex.codecogs.com/svg.image?\widetilde{ct}_{mult}+b= (\overline{c_0}, \overline{c_1}, \overline{c_2})" /> 变换成 <img src="https://latex.codecogs.com/svg.image?{ct}_{mult}=&space;(\overline{c'_0},&space;\overline{c'_1})" title="https://latex.codecogs.com/svg.image?{ct}_{mult}= (\overline{c'_0}, \overline{c'_1})" /> 且保证 <img src="https://latex.codecogs.com/svg.image?\overline{c_0}&plus;\overline{c_1}s&plus;\overline{c_2}s^2&space;\approx&space;\overline{c'_0}&plus;\overline{c'_1}s" title="https://latex.codecogs.com/svg.image?\overline{c_0}+\overline{c_1}s+\overline{c_2}s^2 \approx \overline{c'_0}+\overline{c'_1}s" /> 。

那么，relinearization算法分两步进行：

1. 第一步，对 <img src="https://latex.codecogs.com/svg.image?\mathbf{c_{2}}" title="https://latex.codecogs.com/svg.image?\mathbf{c_{2}}" /> 做decompose得 <img src="https://latex.codecogs.com/svg.image?\xi_q(\mathbf{c_2})=(\mathbf{c_{2,1}},\cdots,&space;\mathbf{c_{2,k}})" title="https://latex.codecogs.com/svg.image?\xi_q(\mathbf{c_2})=(\mathbf{c_{2,1}},\cdots, \mathbf{c_{2,k}})" /> 。
这里有 <img src="https://latex.codecogs.com/svg.image?\mathbf{c_{2,i}}=|\mathbf{c_2}\frac{q_i}{q}|_{q_i}" title="https://latex.codecogs.com/svg.image?\mathbf{c_{2,i}}=|\mathbf{c_2}\frac{q_i}{q}|_{q_i}" /> 。

2. 第二步，计算 <img src="https://latex.codecogs.com/svg.image?(\overline{\mathbf{c}_0},&space;\overline{\mathbf{c}_1})&space;&plus;&space;\sum_i&space;\mathbf{c}_{2,i}\cdot(rlk_i[0],&space;rlk_i[1])" title="https://latex.codecogs.com/svg.image?(\overline{\mathbf{c}_0}, \overline{\mathbf{c}_1}) + \sum_i \mathbf{c}_{2,i}\cdot(rlk_i[0], rlk_i[1])" />

最后描述RNS下的relinearization新算法。注意算法的输入需要调整为RNS数制下的 <img src="https://latex.codecogs.com/svg.image?(\overline{\mathbf{c}_0},&space;\overline{\mathbf{c}_1},&space;\overline{\mathbf{c}_2})&space;" title="https://latex.codecogs.com/svg.image?(\overline{\mathbf{c}_0}, \overline{\mathbf{c}_1}, \overline{\mathbf{c}_2}) " /> 以及 <img src="https://latex.codecogs.com/svg.image?rlk_i" title="https://latex.codecogs.com/svg.image?rlk_i" />，即 <img src="https://latex.codecogs.com/svg.image?\overline{\mathbf{c}_i}&space;=&space;(|\overline{\mathbf{c}_i}|_{q_1},\cdots,|\overline{\mathbf{c}_i}|_{q_k})" title="https://latex.codecogs.com/svg.image?\overline{\mathbf{c}_i} = (|\overline{\mathbf{c}_i}|_{q_1},\cdots,|\overline{\mathbf{c}_i}|_{q_k})" /> 和 <img src="https://latex.codecogs.com/svg.image?rlk_i=(|rlk_i|_{q_1},\cdots,|rlk_i|_{q_k})" title="https://latex.codecogs.com/svg.image?rlk_i=(|rlk_i|_{q_1},\cdots,|rlk_i|_{q_k})" /> 。

那么relinearization第一步decompose应调整为 <img src="https://latex.codecogs.com/svg.image?\overline{\mathbf{c}_{2,i}}=(|\overline{\mathbf{c}_{2,i}}|_{q_1},\cdots,|\overline{\mathbf{c}_{2,i}}|_{q_k})&space;" title="https://latex.codecogs.com/svg.image?\overline{\mathbf{c}_{2,i}}=(|\overline{\mathbf{c}_{2,i}}|_{q_1},\cdots,|\overline{\mathbf{c}_{2,i}}|_{q_k}) " /> ，其中 <img src="https://latex.codecogs.com/svg.image?|\overline{\mathbf{c}_{2,i}}|_{q_j}=\left|\left||c_2|_{q_i}\cdot&space;|\frac{q_i}{q}|_{q_i}\right|_{q_i}\right|_{q_j}" title="https://latex.codecogs.com/svg.image?|\overline{\mathbf{c}_{2,i}}|_{q_j}=\left|\left||c_2|_{q_i}\cdot |\frac{q_i}{q}|_{q_i}\right|_{q_i}\right|_{q_j}" /> 。

第二步直接按RNS加法和乘法(也就是向量加法，和向量对应点乘法)计算即可。

### 完整描述 RNS-FV-multiplication算法

现在从整体上描述RNS版本的BFV同态乘法算法（算法3）：
<p align="center">
  <img src="fig/RNS_fv_alg3.PNG" alt="animated"/>
</p>

对算法3做一些注释。S0做的其实是蒙哥马利形式的FastBconv, 即 <img src="https://latex.codecogs.com/svg.image?FastBconv_{Montgomery}(\mathbf{ct_i},q,\mathcal{B}_{sk}\cup\{\widetilde{m}\})=\mathbf{ct_i''}=|[\widetilde{m}\mathbf{ct_i}]_{q}&plus;q\mathbf{u}|_{m\in\mathcal{B}_{sk}\cup\{\widetilde{m}\}}" title="https://latex.codecogs.com/svg.image?FastBconv_{Montgomery}(\mathbf{ct_i},q,\mathcal{B}_{sk}\cup\{\widetilde{m}\})=\mathbf{ct_i''}=|[\widetilde{m}\mathbf{ct_i}]_{q}+q\mathbf{u}|_{m\in\mathcal{B}_{sk}\cup\{\widetilde{m}\}}" /> ；S1做蒙哥马利约减得到 <img src="https://latex.codecogs.com/svg.image?\mathbf{ct_i'}=|[\mathbf{ct_i}]_{q}|_{m\in\mathcal{B}_{sk}}" title="https://latex.codecogs.com/svg.image?\mathbf{ct_i'}=|[\mathbf{ct_i}]_{q}|_{m\in\mathcal{B}_{sk}}" />; S3-S4做的是RNS版本的division & rounding操作，即先在基 <img src="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" title="https://latex.codecogs.com/svg.image?\mathcal{B}_{sk}" /> 做DR, 然后转换成基q下表示的 <img src="https://latex.codecogs.com/svg.image?\lfloor\frac{t\cdot&space;ct'_{\star}[j]}{q}\rceil" title="https://latex.codecogs.com/svg.image?\lfloor\frac{t\cdot ct'_{\star}[j]}{q}\rceil" /> ；S5做的是RNS版本的CRT-based relinearization。

## 一些重要的证明
