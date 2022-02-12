## FHEW介绍

FHEW是开启第三代FHE的标志性方案。该方案主要是Leo Ducas和Daniele Micciancio在2014年提出并设计。原始论文参考[这里](https://eprint.iacr.org/2014/816.pdf)。与第二代FHE方案相比，bootstrapping的性能得到大幅度提升，在常见的台式机平台上速度可以达到毫秒级别；但同时因为缺少第二代FHE的SIMD特性，FHEW只能处理若干比特的加法和乘法操作，也就是说同态乘法的性能较差。

### 预备知识
#### Learning With Errors (LWE) 对称加密
这里将对明文m的LWE加密标注成![equation](https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}(m)=(\mathbf{a},b)). 其中 <img src="https://latex.codecogs.com/svg.image?\mathbf{b}=<\mathbf{a},\mathbf{s}>&plus;e&plus;\frac{q}{t}m&space;\bmod&space;q" title="\mathbf{b}=<\mathbf{a},\mathbf{s}>+e+\frac{q}{t}m \bmod q" />

已知密钥![equation](https://latex.codecogs.com/svg.image?\vec{s})，则可以对![equation](https://latex.codecogs.com/svg.image?LWE_{\vec{s}}(m)=(\vec{a},b))做解密操作，算法如下：

<p align="center">
<img src="https://latex.codecogs.com/svg.image?\left\lfloor&space;t(b-\mathbf{a}\cdot&space;\mathbf{s})/q\right\rceil&space;\bmod&space;t&space;=&space;\left\lfloor&space;\frac{t}{q}\cdot&space;(\frac{q}{t}m&plus;e)\right\rceil&space;=&space;\left\lfloor&space;m&plus;\frac{t}{q}e\right\rceil&space;=&space;m\bmod&space;t" title="\left\lfloor t(b-\mathbf{a}\cdot \mathbf{s})/q\right\rceil \bmod t = \left\lfloor \frac{t}{q}\cdot (\frac{q}{t}m+e)\right\rceil = \left\lfloor m+\frac{t}{q}e\right\rceil = m\bmod t" />
</p>

#### 模数变换（Modular Switching） 和 密钥变换 （Key Switching）
这里引入FHE方案中的两个重要基本操作Modular Switching(M.S.) 和 Key Switching(K.S.)。它们会反复地出现在FHE系列的文章中。我们不加证明的使用如下结论：

<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE^{t/Q}_{\mathbf{s}}(m)&space;\xrightarrow[]{\text{Modular&space;Switching}}LWE^{t/q}_{\mathbf{s}}(m)&space;" title="LWE^{t/Q}_{\mathbf{s}}(m) \xrightarrow[]{\text{Modular Switching}}LWE^{t/q}_{\mathbf{s}}(m) " />
</p>

<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE^{t/q}_{\mathbf{z}}(m)&space;\xrightarrow[]{\text{Key&space;Switching}}LWE^{t/q}_{\mathbf{s}}(m)&space;" title="LWE^{t/q}_{\mathbf{z}}(m) \xrightarrow[]{\text{Key Switching}}LWE^{t/q}_{\mathbf{s}}(m) " />
</p>

也就是说模数变换可以把LWE instance的大模数Q变换成小模数q(Q>q)，而不改变加密的明文m以及密钥s；
密钥变换则把LWE instance的密钥从原先的向量z变成向量s，而不改变模数q和明文m。

### FHEW顶层逻辑结构
FHEW方案的输入是两个比特的密文<img src="https://bit.ly/3BcPw7P" align="center" border="0" alt="LWE(m_0)=c_0=(\mathbf{a_0},b_0),  LWE(m_1)=c_1=(\mathbf{a_1},b_1)" width="375" height="19" />，输出的是对这两个加密比特进行一次同态门运算得到相应比特的密文。

#### 同态与非门逻辑(Homomorphic NAND gate)
特别地，与非门逻辑是研究的重点。因为实现了与非门，实际上等同于实现了其他所有逻辑(universal logic)。话句话说，我们希望构造这样的同态与非逻辑实现如下运算:

<p align="center">
<img src="http://www.sciweavers.org/tex2img.php?eq=LWE%5E%7Bt%2Fq%7D_%7B%5Cmathbf%7Bs%7D%7D%28m_0%29%2C%20LWE%5E%7Bt%2Fq%7D_%7B%5Cmathbf%7Bs%7D%7D%28m_1%29%20%5Cxrightarrow%5B%5D%7B%5Ctext%7BHomoNAND%7D%7DLWE%5E%7Bt%2Fq%7D_%7B%5Cmathbf%7Bs%7D%7D%28%5Coverline%7Bm_0%5Cwedge%20m_1%7D%29&bc=White&fc=Black&im=jpg&fs=12&ff=modern&edit=0" align="center" border="0" alt="LWE^{t/q}_{\mathbf{s}}(m_0), LWE^{t/q}_{\mathbf{s}}(m_1) \xrightarrow[]{\text{HomoNAND}}LWE^{t/q}_{\mathbf{s}}(\overline{m_0\wedge m_1})" width="408" height="24" />
</p>

这里我们忽略[原始论文](https://eprint.iacr.org/2014/816.pdf)中的相关描述，转而使用[论文](https://eprint.iacr.org/2020/086.pdf)中关于同态查找表(look-up table, LUT)的描述，该表述更富有直觉性，同时也被后续研究发展成functional bootstrap。

基本思路如下，首先我们应该知道同态加法是容易做的，即 <img src="http://www.sciweavers.org/tex2img.php?eq=LWE%5E%7Bt%2Fq%7D_%7B%5Cmathbf%7Bs%7D%7D%28m_0%29%2BLWE%5E%7Bt%2Fq%7D_%7B%5Cmathbf%7Bs%7D%7D%28m_1%29%3DLWE%5E%7Bt%2Fq%7D_%7B%5Cmathbf%7Bs%7D%7D%28m_0%2Bm_1%29%20&bc=White&fc=Black&im=jpg&fs=12&ff=modern&edit=0" align="center" border="0" alt="LWE^{t/q}_{\mathbf{s}}(m_0)+LWE^{t/q}_{\mathbf{s}}(m_1)=LWE^{t/q}_{\mathbf{s}}(m_0+m_1) " width="361" height="22" />。接下来的目标是同态地将<img src="http://www.sciweavers.org/tex2img.php?eq=m_0%2Bm_1&bc=White&fc=Black&im=jpg&fs=12&ff=arev&edit=0" align="center" border="0" alt="m_0+m_1" width="72" height="15" />映射成<img src="http://www.sciweavers.org/tex2img.php?eq=%5Coverline%7Bm_0%5Cwedge%20m_1%7D&bc=White&fc=Black&im=jpg&fs=12&ff=arev&edit=0" align="center" border="0" alt="\overline{m_0\wedge m_1}" width="75" height="18" />，该映射可以用下面表格表示：

(a,b) | a+b  | NAND(a,b)
----  | ---- | ----
(0,0) | 0    | 1
(0,1) | 1    | 1
(1,0) | 1    | 1
(1,1) | 2    | 0

也就是说，这里的重难点是如何同态地构造这样的LUT函数f，可以把0映射成1，1映射成1，2映射成0。


#### 同态累加器(Homomorphic Accumulator)
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
  因此ACC的首要目标是同态地做这个线性操作，这个目标可以利用Initialize和Update操作完成：首先调用Initialize操作1次做<img src="https://latex.codecogs.com/svg.image?ACC&space;\xleftarrow[]{}&space;b" title="ACC \xleftarrow[]{} b" /> 将ACC初始化成b; 接着调用Update操作n次做<img src="https://latex.codecogs.com/svg.image?ACC&space;\xleftarrow[]{&plus;}&space;a_i\cdot&space;E(s_i)" title="ACC \xleftarrow[]{+} a_i\cdot E(s_i)" />，此时可得<img src="https://latex.codecogs.com/svg.image?ACC[b-\sum_i&space;a_is_i]=ACC[\widetilde{m}]" title="ACC[b-\sum_i a_is_i]=ACC[\widetilde{m}]" />
