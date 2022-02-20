# FHEW介绍

FHEW是开启第三代FHE的标志性方案。该方案主要是Leo Ducas和Daniele Micciancio在2014年提出并设计。原始论文参考[这里](https://eprint.iacr.org/2014/816.pdf)。与第二代FHE方案相比，bootstrapping的性能得到大幅度提升，在常见的台式机平台上速度可以达到毫秒级别；但同时因为缺少第二代FHE的SIMD特性，FHEW只能处理若干比特的加法和乘法操作，也就是说同态乘法的性能较差。

## 预备知识
### Learning With Errors (LWE) 对称加密
这里将对明文m的LWE加密标注成![equation](https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}(m)=(\mathbf{a},b)). 其中 <img src="https://latex.codecogs.com/svg.image?\mathbf{b}=<\mathbf{a},\mathbf{s}>&plus;e&plus;\frac{q}{t}m&space;\bmod&space;q" title="\mathbf{b}=<\mathbf{a},\mathbf{s}>+e+\frac{q}{t}m \bmod q" />

已知密钥![equation](https://latex.codecogs.com/svg.image?\vec{s})，则可以对![equation](https://latex.codecogs.com/svg.image?LWE_{\vec{s}}(m)=(\vec{a},b))做解密操作，算法如下：

<p align="center">
<img src="https://latex.codecogs.com/svg.image?\left\lfloor&space;t(b-\mathbf{a}\cdot&space;\mathbf{s})/q\right\rceil&space;\bmod&space;t&space;=&space;\left\lfloor&space;\frac{t}{q}\cdot&space;(\frac{q}{t}m&plus;e)\right\rceil&space;=&space;\left\lfloor&space;m&plus;\frac{t}{q}e\right\rceil&space;=&space;m\bmod&space;t" title="\left\lfloor t(b-\mathbf{a}\cdot \mathbf{s})/q\right\rceil \bmod t = \left\lfloor \frac{t}{q}\cdot (\frac{q}{t}m+e)\right\rceil = \left\lfloor m+\frac{t}{q}e\right\rceil = m\bmod t" />
</p>

### 模数变换（Modular Switching） 和 密钥变换 （Key Switching）
这里引入FHE方案中的两个重要基本操作Modular Switching(M.S.) 和 Key Switching(K.S.)。它们会反复地出现在FHE系列的文章中。我们不加证明的使用如下结论：

<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE^{t/Q}_{\mathbf{s}}(m)&space;\xrightarrow[]{\text{Modular&space;Switching}}LWE^{t/q}_{\mathbf{s}}(m)&space;" title="LWE^{t/Q}_{\mathbf{s}}(m) \xrightarrow[]{\text{Modular Switching}}LWE^{t/q}_{\mathbf{s}}(m) " />
</p>

<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE^{t/q}_{\mathbf{z}}(m)&space;\xrightarrow[]{\text{Key&space;Switching}}LWE^{t/q}_{\mathbf{s}}(m)&space;" title="LWE^{t/q}_{\mathbf{z}}(m) \xrightarrow[]{\text{Key Switching}}LWE^{t/q}_{\mathbf{s}}(m) " />
</p>

也就是说模数变换可以把LWE instance的大模数Q变换成小模数q(Q>q)，而不改变加密的明文m以及密钥s；
密钥变换则把LWE instance的密钥从原先的向量z变成向量s，而不改变模数q和明文m。

## FHEW顶层逻辑结构
FHEW方案的输入是两个比特的密文<img src="https://bit.ly/3BcPw7P" align="center" border="0" alt="LWE(m_0)=c_0=(\mathbf{a_0},b_0),  LWE(m_1)=c_1=(\mathbf{a_1},b_1)" width="375" height="19" />，输出的是对这两个加密比特进行一次同态门运算得到相应比特的密文。

### 同态与非门逻辑(Homomorphic NAND gate)
特别地，与非门逻辑是研究的重点。因为实现了与非门，实际上等同于实现了其他所有逻辑(universal logic)。话句话说，我们希望构造这样的同态与非逻辑实现如下运算:

<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE^{t/q}_{\mathbf{s}}(m_0),&space;LWE^{t/q}_{\mathbf{s}}(m_1)&space;\xrightarrow[]{\text{HomoNAND}}LWE^{t/q}_{\mathbf{s}}(\overline{m_0\wedge&space;m_1})" title="LWE^{t/q}_{\mathbf{s}}(m_0), LWE^{t/q}_{\mathbf{s}}(m_1) \xrightarrow[]{\text{HomoNAND}}LWE^{t/q}_{\mathbf{s}}(\overline{m_0\wedge m_1})" />
</p>

这里我们忽略[原始论文](https://eprint.iacr.org/2014/816.pdf)中的相关描述，转而使用[论文](https://eprint.iacr.org/2020/086.pdf)中关于同态查找表(look-up table, LUT)的描述，该表述更富有直觉性，同时也被后续研究发展成functional bootstrap。

基本思路如下，首先我们应该知道同态加法是容易做的，即<img src="https://latex.codecogs.com/svg.image?LWE^{t/q}_{\mathbf{s}}(m_0)&plus;LWE^{t/q}_{\mathbf{s}}(m_1)=LWE^{t/q}_{\mathbf{s}}(m_0&plus;m_1)" title="LWE^{t/q}_{\mathbf{s}}(m_0)+LWE^{t/q}_{\mathbf{s}}(m_1)=LWE^{t/q}_{\mathbf{s}}(m_0+m_1)" />。接下来的目标是同态地将<img src="https://latex.codecogs.com/svg.image?m_0&plus;m_1" title="m_0+m_1" /> 映射成 <img src="https://latex.codecogs.com/svg.image?\overline{m_0\wedge&space;m_1}" title="\overline{m_0\wedge m_1}" />，该映射可以用下面表格表示：

(a,b) | a+b  | NAND(a,b)
----  | ---- | ----
(0,0) | 0    | 1
(0,1) | 1    | 1
(1,0) | 1    | 1
(1,1) | 2    | 0

也就是说，这里的重难点是如何同态地构造这样的LUT函数f，可以把0映射成1，1映射成1，2映射成0。


### 同态累加器(Homomorphic Accumulator)
这里定义累加器ACC的三个基本操作如下：
1. Initialize: <img src="https://latex.codecogs.com/svg.image?ACC\gets&space;b" title="ACC\gets b" />,将ACC存储内容初始化成一个已知数值b
2. Update: <img src="https://latex.codecogs.com/svg.image?ACC&space;\xleftarrow[]{&plus;}&space;c\cdot&space;E(s)" title="ACC \xleftarrow[]{+} c\cdot E(s)" />, 以累加的方式对ACC进行更新，即将<img src="https://latex.codecogs.com/svg.image?ACC[v]" title="ACC[v]" />变换为<img src="https://latex.codecogs.com/svg.image?ACC[v&plus;c\cdot&space;s]" title="ACC[v+c\cdot s]" />
3. Extract: <img src="https://latex.codecogs.com/svg.image?f(ACC)" title="f(ACC)" />, 若当前ACC存的是v,即ACC[v]，那么f(ACC)表示的对f(v)的加密，即<img src="https://latex.codecogs.com/svg.image?&space;\tilde{E}&space;(f(v))" title=" \tilde{E} (f(v))" />。

注意到Update操作中的s是LWE密钥不可以暴露，否则整个密码系统安全性就回崩溃。因此需要对s进行一次加密变换成E(s)后发布。这个对s进行加密生成的密文在FHE语境里称之为bootstrapping key（有的文章称之为evaluation key或者refreshing key）

<p align="center">
  <img src="https://latex.codecogs.com/svg.image?ek&space;=&space;E(\mathbf{s})=(E(s_1),\cdots,E(s_n))" title="ek = E(\mathbf{s})=(E(s_1),\cdots,E(s_n))" />
 </p>
 
 接着，我们尝试理解“ACC的三个操作为什么可以完成bootstrapping？”这个问题。Bootstrapping的本质是同态地做LWE解密。注意到LWE解密操作的第一步需要做一次这样的线性操作使得：
 <p align="center">
  <img src="https://latex.codecogs.com/svg.image?\widetilde{m}=b-\mathbf{a}\cdot&space;\mathbf{s}=\frac{q}{t}m&plus;e\approx&space;\frac{q}{t}m" title="\widetilde{m}=b-\mathbf{a}\cdot \mathbf{s}=\frac{q}{t}m+e\approx \frac{q}{t}m" />
   </p>
  因此ACC的首要目标是同态地做这个线性操作，这个目标可以利用Initialize和Update操作完成：首先调用Initialize操作1次做:
  <p align="center">
  <img src="https://latex.codecogs.com/svg.image?ACC&space;\xleftarrow[]{}&space;b" title="ACC \xleftarrow[]{} b" /> 
  </p>
  将ACC初始化成b; 接着调用Update操作n次做:
  <p align="center">
  <img src="https://latex.codecogs.com/svg.image?ACC&space;\xleftarrow[]{&plus;}&space;-a_i\cdot&space;E(s_i)" title="ACC \xleftarrow[]{+} -a_i\cdot E(s_i)" />
  </p>
  此时可得我们想要的形式，即：
  <p align="center">
  <img src="https://latex.codecogs.com/svg.image?ACC[b-\sum_i&space;a_is_i]=ACC[\widetilde{m}]" title="ACC[b-\sum_i a_is_i]=ACC[\widetilde{m}]" />
  </p>
  
  到了这一步，假如我们可以同态的计算映射函数f（f也被称之为rounding function,如何构造它是FHEW方案中的难点部分，会在稍微章节介绍),那么就完成了整个同态异或的运算，即:
    <p align="center">
  <img src="https://latex.codecogs.com/svg.image?f(ACC(\widetilde{m}))\to&space;ACC(f(\widetilde{m}))" title="f(ACC(\widetilde{m}))\to ACC(f(\widetilde{m}))" />
    </p>
    
  利用同态累加器ACC,我们用下面这个算法([算法1](fig/alg1.png))来描述FHEW bootstrapping的整个过程：
  <p align="center">
  <img src="fig/alg1.png" alt="animated" />
   </p>
   至此，我们从顶层描述了FHEW bootstrapping的原理。下面对ACC的三个基本操作initialize, update, 和 extract做更详细的阐述。
   
   
   #### 初始化 Initialize
   在初始化过程中，需要将LUT函数f也编码进去，即<img src="https://latex.codecogs.com/svg.image?ACC_f&space;\gets&space;v" title="ACC_f \gets v" />。
   这里使用无噪声的RLWE加密m，即
    <p align="center">
   <img src="https://latex.codecogs.com/svg.image?ACC_f&space;=&space;RLWE(m)=(0,m(X))=(0,&space;\sum_{i=0}^{q/2-1}f(v-i)\cdot&space;X^i)" title="ACC_f = RLWE(m)=(0,m(X))=(0, \sum_{i=0}^{q/2-1}f(v-i)\cdot X^i)" />
    </p>
  <div>注意这里<img src="https://latex.codecogs.com/svg.image?m\in&space;\mathbb{Z}_Q[X]/(X^{q/2}&plus;1)" title="m\in \mathbb{Z}_Q[X]/(X^{q/2}+1)" />,但实际上FHEW将m定义在一个更大的整数多项式环<img src="https://latex.codecogs.com/svg.image?\mathbb{Z}_Q[X]/(X^{N}&plus;1)" title="\mathbb{Z}_Q[X]/(X^{N}+1)" />使得<img src="https://latex.codecogs.com/svg.image?\mathbb{Z}_Q[X]/(X^{q/2}&plus;1)" title="\mathbb{Z}_Q[X]/(X^{q/2}+1)" />是它的子环。使用更大的环的原因是安全性考虑。只有环足够大才能使得相应的RLWE问题足够难从而达到相应的安全级别。</div>
  
  #### 更新 Update
Update操作的核心是同态乘法算法（<img src="https://latex.codecogs.com/svg.image?RLWE\times&space;RGSW\to&space;RLWE" title="RLWE\times RGSW\to RLWE" />）。
注意bootstrapping key的实质是对LWE密钥进行加密。更具体地说，对LWE密钥向量中的每一个元素，即<img src="https://latex.codecogs.com/svg.image?s\in&space;\mathbb{Z}_q&space;\overset{element-select}{\leftarrow}\mathbf{s}\in&space;\mathbb{Z}_q^n" title="s\in \mathbb{Z}_q \overset{element-select}{\leftarrow}\mathbf{s}\in \mathbb{Z}_q^n" />, 进行GSW加密得到:
 <p align="center">
<img src="https://latex.codecogs.com/svg.image?E(s)=\{\mathbf{Z_{j,v}}=RGSW(X^{vB_r^j\cdot&space;s})|j<log_{B_r}q,v\in&space;\mathbb{Z}_{B_r}\}" title="E(s)=\{\mathbf{Z_{j,v}}=RGSW(X^{vB_r^j\cdot s})|j<log_{B_r}q,v\in \mathbb{Z}_{B_r}\}" />
 </p>
<div>那么，Update操作<img src="https://latex.codecogs.com/svg.image?ACC&space;\xleftarrow[]{&plus;}&space;c\cdot&space;E(s)" title="ACC \xleftarrow[]{+} c\cdot E(s)" />可以按以下流程计算得到：</div>

1. <div>将c"扁平化"得到<img src="https://latex.codecogs.com/svg.image?c=\sum_i&space;B_r^ic_i" title="c=\sum_i B_r^ic_i" /></div>
2. 依次对所有的j,做同态乘法操作<img src="https://latex.codecogs.com/svg.image?ACC\gets&space;ACC&space;\diamond&space;\mathbf{Z}_{j,c_j}" title="ACC\gets ACC \diamond \mathbf{Z}_{j,c_j}" />

[算法2](fig/alg2.png)形式化地描述Update操作如下图所示。
  <p align="center">
  <img src="fig/alg2.png" alt="animated" />
   </p>
   
  #### 提取 Extraction
  在提取过程中，LUT function（也称之为rounding function）是至关重要的。现在考虑FHEW的输入密文的具体形式。它满足 <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{4/q}(m)=(\mathbf{a},b),&space;|e|=|b-\mathbf{a}\cdot&space;\mathbf{s}-\frac{q}{4}m|<q/16,&space;m\in\{0,1\}" title="LWE_{\mathbf{s}}^{4/q}(m)=(\mathbf{a},b), |e|=|b-\mathbf{a}\cdot \mathbf{s}-\frac{q}{4}m|<q/16, m\in\{0,1\}" />
  
  同态NAND逻辑的输入是上面形式的两个LWE密文，即 <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{4/q}(m_0)=(\mathbf{a_0},b_0)" title="LWE_{\mathbf{s}}^{4/q}(m_0)=(\mathbf{a_0},b_0)" /> 和 <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{4/q}(m_1)=(\mathbf{a_1},b_1)" title="LWE_{\mathbf{s}}^{4/q}(m_1)=(\mathbf{a_1},b_1)" />。首先将两端密文相加得 
   <p align="center">
  <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{4/q}(m_0&plus;m_1)=(\mathbf{a},b)=(\mathbf{a_0}&plus;\mathbf{a_1},b_0&plus;b_1),\\|e|=|e_0&plus;e_1|<q/8,&space;\\m_0&plus;m_1\in\{0,1,2\}" title="LWE_{\mathbf{s}}^{4/q}(m_0+m_1)=(\mathbf{a},b)=(\mathbf{a_0}+\mathbf{a_1},b_0+b_1),\\|e|=|e_0+e_1|<q/8, \\m_0+m_1\in\{0,1,2\}" />
   </p>
   
   那么rounding function f需满足
   1. <img src="https://latex.codecogs.com/svg.image?\frac{q}{4}(m_0&plus;m_1)&plus;e&space;\\" title="\frac{q}{4}(m_0+m_1)+e \\" /> 舍入到最近的 <img src="https://latex.codecogs.com/svg.image?\frac{q}{4}" title="\frac{q}{4}" /> 的整数倍
   2. 映射规律：<img src="https://latex.codecogs.com/svg.image?0\mapsto&space;1,&space;1\mapsto&space;1,&space;2\mapsto&space;0,(3\mapsto&space;0)" title="0\mapsto 1, 1\mapsto 1, 2\mapsto 0,(3\mapsto 0)" />
   3. 映射之后得到的一比特信息缩放 <img src="https://latex.codecogs.com/svg.image?Q/4" title="Q/4" /> 倍（这是因为extraction最终得到<img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{z}}^{Q/4}(\cdot)" title="LWE_{\mathbf{z}}^{Q/4}(\cdot)" /> 这样的形式，需要做一次Key-Switching和一次Modular-Switching 恢复成 <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/4}(\cdot)" title="LWE_{\mathbf{s}}^{q/4}(\cdot)" /> ）

总而言之，函数f应当将区间 <img src="https://latex.codecogs.com/svg.image?[0,q/4]\pm&space;q/8=(-q/8,3q/8)" title="[0,q/4]\pm q/8=(-q/8,3q/8)" /> 映射到 <img src="https://latex.codecogs.com/svg.image?q/4" title="q/4" />；<img src="https://latex.codecogs.com/svg.image?[2q/4,3q/4]\pm&space;q/8&space;=&space;(3q/8,7q/8)" title="[2q/4,3q/4]\pm q/8 = (3q/8,7q/8)" /> 映射到 <img src="https://latex.codecogs.com/svg.image?0" title="0" />。但这里存在一个问题：f必须满足负周期性f(v+q/2)=-f(v)，因此上述映射关系不能直接满足。解决方案是把 <img src="https://latex.codecogs.com/svg.image?[0,q/4]\pm&space;q/8=(-q/8,3q/8)" title="[0,q/4]\pm q/8=(-q/8,3q/8)" /> 映射到 <img src="https://latex.codecogs.com/svg.image?q/8" title="q/8" />，<img src="https://latex.codecogs.com/svg.image?[2q/4,3q/4]\pm&space;q/8&space;=&space;(3q/8,7q/8)" title="[2q/4,3q/4]\pm q/8 = (3q/8,7q/8)" /> 映射到 <img src="https://latex.codecogs.com/svg.image?-q/8" title="-q/8" />，这样得到 <img src="https://latex.codecogs.com/svg.image?LWE(\frac{q}{8}(2m-1))" title="LWE(\frac{q}{8}(2m-1))" /> 。最后加入无噪声的LWE密文 <img src="https://latex.codecogs.com/svg.image?(\mathbf{0},q/8)=LWE(q/8)" title="(\mathbf{0},q/8)=LWE(q/8)" /> 得到目标映射:
 <p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE(\frac{q}{8}(2m-1))&plus;&space;LWE(\frac{q}{8})=LWE(\frac{q}{4}m)" title="LWE(\frac{q}{8}(2m-1))+ LWE(\frac{q}{8})=LWE(\frac{q}{4}m)" />
 </p>

到这里我们可以完整地给出详细的FHEW bootstrapping[算法3](fig/alg3.PNG)如下
  <p align="center">
  <img src="fig/alg3.PNG" alt="animated" />
   </p>
<div>   关于算法3的注释：首先讨论EvalKeyGen()。Evaluation Key的本质是用密钥z对密钥s进行加密。因为Bootstrapping中需要做形如 <img src="https://latex.codecogs.com/svg.image?c\cdot&space;E(s)" title="c\cdot E(s)" /> 的计算。为了更好的控制噪声增长，需要对c扁平化，即做 <img src="https://latex.codecogs.com/svg.image?c=\sum_i&space;c_iB_r^i" title="c=\sum_i c_iB_r^i" />。相应的需要存储 <img src="https://latex.codecogs.com/svg.image?E(c_jB_r^j\cdot&space;s)" title="E(c_jB_r^j\cdot s)" />。这样，只需要做几次同态加法即可得到:</div>
  <p align="center">
<img src="https://latex.codecogs.com/svg.image?c\cdot&space;E(s)=E(c\cdot&space;s)=&space;E(\sum_j&space;c_jB_r^j\cdot&space;s)=\sum_jE(c_jB_r^j\cdot&space;s)" title="c\cdot E(s)=E(c\cdot s)= E(\sum_j c_jB_r^j\cdot s)=\sum_jE(c_jB_r^j\cdot s)" />
 </p>
