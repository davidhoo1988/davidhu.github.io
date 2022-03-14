# TFHE方案介绍
这里介绍TFHE方案。它是对FHEW方案的拓展，特别是在改进GSW乘法和FHEW bootstrapping性能上做出了重要贡献。可以说TFHE是第三代FHE方案中性能最好的一个。

## 预备知识
### Torus <img src="https://latex.codecogs.com/svg.image?\mathbb{T}" title="https://latex.codecogs.com/svg.image?\mathbb{T}" />
Torus就是TFHE中T的由来。Torus定义成[0,1)的实数，他在正常的实数运算基础上需要mod 1,即取正常运算结构的小数部分，例如:
0.11+0.92=0.03， 10*0.11=0.1

在TFHE的软件实现中，实际上用的int32整型来表征Torus。换句话说，Torus事实上被当成 <img src="https://latex.codecogs.com/svg.image?\mathbb{Z}_{2^{32}}" title="https://latex.codecogs.com/svg.image?\mathbb{Z}_{2^{32}}" />。

利用ModSwitch可以证明Torus结构和正常的<img src="https://latex.codecogs.com/svg.image?\mathbb{Z}_p" title="https://latex.codecogs.com/svg.image?\mathbb{Z}_p" /> 结构是等价的。所以这篇文章里避免提及Torus从而使得TFHE方案的表述更简洁。

### Cmux Gate
令 <img src="https://latex.codecogs.com/svg.image?&space;\mathbf{C_{2d_g\times&space;2}}=RGSW(C),&space;&space;&space;\mathbf{d_i}=RLWE(m_i),&space;C\in\{0,1\},&space;m_i\in&space;R_{n,q}" title="https://latex.codecogs.com/svg.image? \mathbf{C_{2d_g\times 2}}=RGSW(C), \mathbf{d_i}=RLWE(m_i), C\in\{0,1\}, m_i\in R_{n,q}" />，定义<img src="https://latex.codecogs.com/svg.image?CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0})=RLWE(m_C)" title="https://latex.codecogs.com/svg.image?CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0})=RLWE(m_C)" /> 如下：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0})=\mathbf{C}\diamond&space;(\mathbf{d_1}-\mathbf{d_0})&plus;\mathbf{d_0}" title="https://latex.codecogs.com/svg.image?CMux(\mathbf{C},\mathbf{d_1},\mathbf{d_0})=\mathbf{C}\diamond&space;(\mathbf{d_1}-\mathbf{d_0})&plus;\mathbf{d_0}" />
 </p>
 <div>也就是说,CMux的功能是，给定一个加密状态下的比特C，如果C是1，那么输出m1的密文，否则输出m0的密文。这里关键的是GSW乘法运算 <img src="https://latex.codecogs.com/svg.image?\diamond:RGSW\times&space;RLWE&space;\to&space;RLWE" title="https://latex.codecogs.com/svg.image?\diamond:RGSW\times RLWE \to RLWE" /> （关于GSW乘法，可以参考我的这篇[博文](https://github.com/davidhoo1988/davidhu.github.io/blob/gh-pages/pages/GSW/main.md)</div>
 
### Blind Rotation
基于上面对CMux的介绍，现在可以引入TFHE bootstrap最核心的运算Blind Rotation。

### Constant Extraction

## TFHE bootstrapping
### 基本原理
首先意识到 <img src="https://latex.codecogs.com/svg.image?R_{N,Q}" title="https://latex.codecogs.com/svg.image?R_{N,Q}" /> 的根构成了阶数为2N的乘性循环群(cyclic multiplicative group) <img src="https://latex.codecogs.com/svg.image?\mathbb{G}=\{1,X,\cdots,X^{N-1},-1,\cdots,-X^{N-1}\}" title="https://latex.codecogs.com/svg.image?\mathbb{G}=\{1,X,\cdots,X^{N-1},-1,\cdots,-X^{N-1}\}" />。 TFHE bootstrapping的输入是一个LWE instance <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/t}(m)=(\mathbf{a},\mathbf{a}\cdot&space;\mathbf{s}&plus;e&plus;\frac{qm}{t})=(\mathbf{a},b)" title="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/t}(m)=(\mathbf{a},\mathbf{a}\cdot \mathbf{s}+e+\frac{qm}{t})=(\mathbf{a},b)" />, 为了表述方便这里假定q=2N。引入一个新概念，即旋转多项式(rotation polynomial) <img src="https://latex.codecogs.com/svg.image?rotP(X)=\frac{Q}{t}\cdot(1-\sum_{i=1}^{N-1}X^i)\in&space;R_Q" title="https://latex.codecogs.com/svg.image?rotP(X)=\frac{Q}{t}\cdot(1-\sum_{i=1}^{N-1}X^i)\in R_Q" />，初始化bootstrap的输入为 <img src="https://latex.codecogs.com/svg.image?acc=rotP\cdot&space;X^b\in&space;R_Q" title="https://latex.codecogs.com/svg.image?acc=rotP\cdot X^b\in R_Q" />, 接着对acc(同态地)乘以<img src="https://latex.codecogs.com/svg.image?X^{-a_i\cdot&space;s_i}" title="https://latex.codecogs.com/svg.image?X^{-a_i\cdot s_i}" /> 的密文（这个概念称之为blind rotation，在正式构造中再详细介绍）最终得到以下形式：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?RLWE(rotP\cdot&space;X^{b-\mathbf{a}\cdot&space;\mathbf{s}})=RLWE(rotP\cdot&space;X^{\frac{Q}{t}m&plus;e&space;\bmod&space;2N})" title="https://latex.codecogs.com/svg.image?RLWE(rotP\cdot X^{b-\mathbf{a}\cdot \mathbf{s}})=RLWE(rotP\cdot X^{\frac{Q}{t}m+e \bmod 2N})" />
</p>
<div>注意这里引入rotP的关键原因是：如果 <img src="https://latex.codecogs.com/svg.image?\frac{Q}{t}m&plus;e&space;\in&space;[0,N)&space;" title="https://latex.codecogs.com/svg.image?\frac{Q}{t}m+e \in [0,N) " />，那么多项式 <img src="https://latex.codecogs.com/svg.image?rotP\cdot&space;X^{\frac{Q}{t}m&plus;e&space;\bmod&space;2N}" title="https://latex.codecogs.com/svg.image?rotP\cdot X^{\frac{Q}{t}m+e \bmod 2N}" /> 的常数项一定是 <img src="https://latex.codecogs.com/svg.image?\frac{Q}{t}" title="https://latex.codecogs.com/svg.image?\frac{Q}{t}" />；如果 <img src="https://latex.codecogs.com/svg.image?\frac{Q}{t}m&plus;e&space;\in&space;[N,2N)" title="https://latex.codecogs.com/svg.image?\frac{Q}{t}m+e \in [N,2N)" />，则它的常数项一定是 <img src="https://latex.codecogs.com/svg.image?-\frac{Q}{t}" title="https://latex.codecogs.com/svg.image?-\frac{Q}{t}" />。如果我们可以同态地提取这个常数项，那么实际上就是同态地计算符号函数:</div>
<p align="center">
<img src="https://latex.codecogs.com/svg.image?sgn(\frac{Q}{t}m&plus;e)=\begin{cases}&space;&plus;1&&space;\text{&space;if&space;}&space;\frac{Q}{t}m&plus;e\in[0,N)&space;\\&space;-1&&space;\text{&space;if&space;}&space;\frac{Q}{t}m&plus;e\in[N,2N)&space;\end{cases}" title="https://latex.codecogs.com/svg.image?sgn(\frac{Q}{t}m+e)=\begin{cases} +1& \text{ if } \frac{Q}{t}m+e\in[0,N) \\ -1& \text{ if } \frac{Q}{t}m+e\in[N,2N) \end{cases}" />
 </p>
 
### 正式构造
这里我们以(同态)与非逻辑为例，正式介绍TFHE bootstrap。给定m0和m1的密文，经过bootstrap输出NAND(m0,m1)的密文，即：
<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE_\mathbf{s}^{q/4}(m_0,|e_0|<\frac{q}{16})&plus;LWE_\mathbf{s}^{q/4}(m_1,|e_0|<\frac{q}{16})\xrightarrow[]{bootstrap}&space;LWE_\mathbf{s}^{q/4}(\overline{m_0\wedge&space;m_1&space;})" title="https://latex.codecogs.com/svg.image?LWE_\mathbf{s}^{q/4}(m_0,|e_0|<\frac{q}{16})+LWE_\mathbf{s}^{q/4}(m_1,|e_0|<\frac{q}{16})\xrightarrow[]{bootstrap} LWE_\mathbf{s}^{q/4}(\overline{m_0\wedge m_1 })" />
</p>

首先,同态加法是容易做的，即<img src="https://latex.codecogs.com/svg.image?LWE^{q/4}_{\mathbf{s}}(m_0)&plus;LWE^{q/4}_{\mathbf{s}}(m_1)=LWE^{q/4}_{\mathbf{s}}(m_0&plus;m_1)" title="LWE^{q/4}_{\mathbf{s}}(m_0)+LWE^{q/4}_{\mathbf{s}}(m_1)=LWE^{q/4}_{\mathbf{s}}(m_0+m_1)" />。接下来的目标是同态地将<img src="https://latex.codecogs.com/svg.image?m_0&plus;m_1" title="m_0+m_1" /> 映射成 <img src="https://latex.codecogs.com/svg.image?\overline{m_0\wedge&space;m_1}" title="\overline{m_0\wedge m_1}" />，该映射可以用下面表格表示：

(a,b) | a+b  | NAND(a,b)
----  | ---- | ----
(0,0) | 0    | 1
(0,1) | 1    | 1
(1,0) | 1    | 1
(1,1) | 2    | 0

也就是说，bootstrap的难点是如何同态地把0映射成1，1映射成1，2映射成0。具体而言，需要达成下面三个目标。
   1. <img src="https://latex.codecogs.com/svg.image?\frac{q}{4}(m_0&plus;m_1)&plus;e&space;\\" title="\frac{q}{4}(m_0+m_1)+e \\" /> 舍入到最近的 <img src="https://latex.codecogs.com/svg.image?\frac{q}{4}" title="\frac{q}{4}" /> 的整数倍
   2. 映射规律：<img src="https://latex.codecogs.com/svg.image?0\mapsto&space;1,&space;1\mapsto&space;1,&space;2\mapsto&space;0,(3\mapsto&space;0)" title="0\mapsto 1, 1\mapsto 1, 2\mapsto 0,(3\mapsto 0)" />
   3. 映射之后得到的一比特信息缩放 <img src="https://latex.codecogs.com/svg.image?Q/4" title="Q/4" /> 倍（这是因为extraction最终得到<img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{z}}^{Q/4}(\cdot)" title="LWE_{\mathbf{z}}^{Q/4}(\cdot)" /> 这样的形式，需要做一次Key-Switching和一次Modular-Switching 恢复成 <img src="https://latex.codecogs.com/svg.image?LWE_{\mathbf{s}}^{q/4}(\cdot)" title="LWE_{\mathbf{s}}^{q/4}(\cdot)" /> ）

总而言之，函数f应当将区间 <img src="https://latex.codecogs.com/svg.image?[0,q/4]\pm&space;q/8=(-q/8,3q/8)" title="[0,q/4]\pm q/8=(-q/8,3q/8)" /> 映射到 <img src="https://latex.codecogs.com/svg.image?q/4" title="q/4" />；<img src="https://latex.codecogs.com/svg.image?[2q/4,3q/4]\pm&space;q/8&space;=&space;(3q/8,7q/8)" title="[2q/4,3q/4]\pm q/8 = (3q/8,7q/8)" /> 映射到 <img src="https://latex.codecogs.com/svg.image?0" title="0" />。但这里存在一个问题：旋转多项式rotP必须满足负周期性f(v+q/2)=-f(v)，因此上述映射关系不能直接满足。解决方案是把 <img src="https://latex.codecogs.com/svg.image?[0,q/4]\pm&space;q/8=(-q/8,3q/8)" title="[0,q/4]\pm q/8=(-q/8,3q/8)" /> 映射到 <img src="https://latex.codecogs.com/svg.image?q/8" title="q/8" />，<img src="https://latex.codecogs.com/svg.image?[2q/4,3q/4]\pm&space;q/8&space;=&space;(3q/8,7q/8)" title="[2q/4,3q/4]\pm q/8 = (3q/8,7q/8)" /> 映射到 <img src="https://latex.codecogs.com/svg.image?-q/8" title="-q/8" />，这样得到 <img src="https://latex.codecogs.com/svg.image?LWE(\frac{q}{8}(2m-1))" title="LWE(\frac{q}{8}(2m-1))" /> 。最后加入无噪声的LWE密文 <img src="https://latex.codecogs.com/svg.image?(\mathbf{0},q/8)=LWE(q/8)" title="(\mathbf{0},q/8)=LWE(q/8)" /> 得到目标映射:
 <p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE(\frac{q}{8}(2m-1))&plus;&space;LWE(\frac{q}{8})=LWE(\frac{q}{4}m)" title="LWE(\frac{q}{8}(2m-1))+ LWE(\frac{q}{8})=LWE(\frac{q}{4}m)" />
 </p>
<div>综上所述，TFHE需要的特定形式的旋转多项式 <img src="https://latex.codecogs.com/svg.image?rotP(X)\in&space;R_{Q,q/2}=\mathbb{Z}_Q[X]/(X^{q/2}&plus;1)" title="https://latex.codecogs.com/svg.image?rotP(X)\in R_{Q,q/2}=\mathbb{Z}_Q[X]/(X^{q/2}+1)" /> 为： </div>
  <p align="center">
<img src="https://latex.codecogs.com/svg.image?rotP(X)=\frac{Q}{8}&space;&plus;&space;\cdots&space;&plus;&space;\frac{Q}{8}X^{q/8-1}-&space;\frac{Q}{8}X^{q/8}&space;-&space;\cdots&space;-&space;\frac{Q}{8}X^{5q/8}&space;&plus;&space;\frac{Q}{8}X^{5q/8&plus;1}&space;&plus;&space;\cdots&space;&plus;&space;\frac{Q}{8}X^{q/2-1}" title="rotP(X)=\frac{Q}{8} + \cdots + \frac{Q}{8}X^{q/8-1}- \frac{Q}{8}X^{q/8} - \cdots - \frac{Q}{8}X^{5q/8} + \frac{Q}{8}X^{5q/8+1} + \cdots + \frac{Q}{8}X^{q/2-1}" />
  </p>
   
<div>因此， <img src="https://latex.codecogs.com/svg.image?\frac{q}{4}m&plus;e\in&space;[\frac{3q}{8},\frac{7q}{8}]\to&space;const(rotP\cdot&space;X^{\frac{q}{4}m&plus;e})=-\frac{Q}{8}" title="https://latex.codecogs.com/svg.image?\frac{q}{4}m+e\in [\frac{3q}{8},\frac{7q}{8}]\to const(rotP\cdot X^{\frac{q}{4}m+e})=-\frac{Q}{8}" />，否则 <img src="https://latex.codecogs.com/svg.image?const(rotP\cdot&space;X^{\frac{q}{4}m&plus;e})=\frac{Q}{8}" title="https://latex.codecogs.com/svg.image?const(rotP\cdot X^{\frac{q}{4}m+e})=\frac{Q}{8}" />。</div> 
 但是，我们需要解决这样的问题，因为RLWE安全性的要求，需要rotP定义在一个更大的多项式环上，即
 最后，我们归纳TFHE bootstrap算法步骤如下：
   <p align="center">
  <img src="fig/TFHE_bootstrap.PNG" alt="animated" />
   </p>
   
### 噪声分析
bootstrap操作本身会引入额外噪声。为了保障TFHE bootstrap的正确性，一个关键的问题是分析bootstrap算法的噪声，使得噪声幅值大小在可控范围。
