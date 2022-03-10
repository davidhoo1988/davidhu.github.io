# GSW 介绍
GSW是公认的第一个第三代FHE方案。Craig Gentry, Amit Sahai 和 Brent Waters最先提出该方案，其成果发表在[CRYPTO13](https://eprint.iacr.org/2013/340)。GSW最主要的贡献是探索了一条新的构造同态乘法的路径。在第二代FHE方案中，同态乘法的噪声增加比同态加法要大的多。GSW提出新的构造可以更有效的控制同态乘法运算的噪声增长。作为代价，它的密文长度(矩阵形式)也要大的多。GSW方案是后续的FHEW方案和TFHE方案的基石。FHEW/TFHE正是借用GSW的同态乘法构造了新的bootstrapping算法。

## 预备知识
### RLWE 的基本结构和同态运算
首先，我们回顾RLWE(Learning With Errors over Rings)的代数构造。这里注意区别两个概念：加密(encrypt)和编码(encode)。 RLWE加密有如下形式：
<p align="center">
  <img src="https://latex.codecogs.com/svg.image?RLWE_s(\widetilde{m})=(a,as&plus;e&plus;\widetilde{m})" title="RLWE_s(\widetilde{m})=(a,as+e+\widetilde{m})" />
</p>
<div>这里, <img src="https://latex.codecogs.com/svg.image?\widetilde{m}\in&space;R_q=R/qR=\mathbb{Z}_q[X]/(X^n&plus;1)" title="\widetilde{m}\in R_q=R/qR=\mathbb{Z}_q[X]/(X^n+1)" /> 是编码之后的明文(假定明文 <img src="https://latex.codecogs.com/svg.image?m\in&space;R_t&space;(t\ll&space;q)" title="m\in R_t (t\ll q)" /> , 编码则可以表示为 <img src="https://latex.codecogs.com/svg.image?\widetilde{m}=(q/t)m" title="\widetilde{m}=(q/t)m" /> )；<img src="https://latex.codecogs.com/svg.image?s\in&space;R=\mathbb{Z}[X]/(X^n&plus;1)" title="s\in R=\mathbb{Z}[X]/(X^n+1)" /> 是密钥。具体地，随机均匀地选取 <img src="https://latex.codecogs.com/svg.image?a\gets&space;R_q" title="a\gets R_q" /> ; 随机从离散高斯分布(均值为0，方差为 <img src="https://latex.codecogs.com/svg.image?\sigma" title="\sigma" /> ) 选取 <img src="https://latex.codecogs.com/svg.image?e\gets&space;\chi_{\sigma}^n" title="e\gets \chi_{\sigma}^n" /> ; 随机从某个离散‘窄’分布选取密钥（比如 <img src="https://latex.codecogs.com/svg.image?s\gets&space;\chi_{\sigma}^n" title="s\gets \chi_{\sigma}^n" /> 或 <img src="https://latex.codecogs.com/svg.image?s\gets&space;\{0,1,-1\}^n" title="s\gets \{0,1,-1\}^n" /> ）</div>

相应地，给定一个RLWE密文 <img src="https://latex.codecogs.com/svg.image?(a,b)\in&space;R_q^2" title="(a,b)\in R_q^2" /> ，它的解密形式可以用下式表达
<p align="center">
<img src="https://latex.codecogs.com/svg.image?RLWE_s^{-1}(a,b)=b-as=\widetilde{m}&plus;e" title="RLWE_s^{-1}(a,b)=b-as=\widetilde{m}+e" />
</p>
<div>对进行解码(decode)恢复出最后的明文(前提条件是 <img src="https://latex.codecogs.com/svg.image?|e|<q/2t" title="|e|<q/2t" /> )：</div>
<p align="center">
<img src="https://latex.codecogs.com/svg.image?m=\lfloor\frac{t}{q}(\widetilde{m}&plus;e)\rceil" title="m=\lfloor\frac{t}{q}(\widetilde{m}+e)\rceil" />
</p>

接着，我们回顾RLWE密文的同态加法操作。

<p align="center">
<img src="https://latex.codecogs.com/svg.image?LWE_s(m_0)&plus;LWE_s(m_1)=(a_0,b_0)&plus;(a_1,b_1)=(a_0&plus;a_1,b_0&plus;b_1)=LWE_s(m_0&plus;m_1)" title="LWE_s(m_0)+LWE_s(m_1)=(a_0,b_0)+(a_1,b_1)=(a_0+a_1,b_0+b_1)=LWE_s(m_0+m_1)" />
</p>
类似地，可得同态标量乘法操作。

<p align="center">
<img src="https://latex.codecogs.com/svg.image?d\cdot&space;LWE_s(m_0)=d\cdot&space;(a_0,b_0)=(d\cdot&space;a_0,d\cdot&space;b_0)=LWE_s(d\cdot&space;m_0)" title="d\cdot LWE_s(m_0)=d\cdot (a_0,b_0)=(d\cdot a_0,d\cdot b_0)=LWE_s(d\cdot m_0)" />
</p>

令 <img src="https://latex.codecogs.com/svg.image?\mathbf{c_0}&space;=&space;(a_0,b_0)=LWE_s(m_0),\mathbf{c_1}&space;=&space;(a_1,b_1)=LWE_s(m_1)" title="\mathbf{c_0} = (a_0,b_0)=LWE_s(m_0),\mathbf{c_1} = (a_1,b_1)=LWE_s(m_1)" /> 分析加法操作和标量乘操作的噪声增长如下:
<p align="center">
<img src="https://latex.codecogs.com/svg.image?Err_s(\mathbf{c_0}&plus;\mathbf{c_1})=Err_s(\mathbf{c_0})&plus;Err_s(\mathbf{c_1})" title="Err_s(\mathbf{c_0}+\mathbf{c_1})=Err_s(\mathbf{c_0})+Err_s(\mathbf{c_1})" />
</p>
<p align="center">
<img src="https://latex.codecogs.com/svg.image?Err_s(d\cdot&space;\mathbf{c_0})=d\cdot&space;Err_s(\mathbf{c_0})" title="Err_s(d\cdot \mathbf{c_0})=d\cdot Err_s(\mathbf{c_0})" />
</p>
注意到上面的标量乘操作要求d是一个较小的数，否则噪声增长超出允许的上界就会出现解密错误。显然，一个自然的问题是: 我们可不可以构造更好的标量乘操作使得对任何大小的d都适用呢？
为了解决该问题，我们定义一个新的LWE加密形式:

<p align="center">
<img src="https://latex.codecogs.com/svg.image?RLWE'_s(m)=\left(RLWE_s(m),RLWE_s(Bm),RLWE_s(B^2m),\cdots,RLWE_s(B^{k-1}m)\right)" title="RLWE'_s(m)=\left(RLWE_s(m),RLWE_s(Bm),RLWE_s(B^2m),\cdots,RLWE_s(B^{B^{k-1}}m)\right)" />
</p>
<div>接着，对标量d进行基于B的扁平化操作 <img src="http://latex.codecogs.com/svg.latex?d=\sum_iB^id_i" title="http://latex.codecogs.com/svg.latex?d=\sum_iB^id_i" />。令 <img src="http://latex.codecogs.com/svg.latex?RLWE(B^im)=\mathbf{c}_i" title="http://latex.codecogs.com/svg.latex?RLWE(B^im)=\mathbf{c}_i" /> , 定义新的标量乘法运算如下: </div>

<p align="center">
<img src="http://latex.codecogs.com/svg.latex?d\odot&space;(\mathbf{c_0},\cdots,\mathbf{c_{k-1}})=\sum_id_i\cdot\mathbf{c_i}" title="http://latex.codecogs.com/svg.latex?d\odot (\mathbf{c_0},\cdots,\mathbf{c_{k-1}})=\sum_id_i\cdot\mathbf{c_i}" />
</p>
<div>容易发现，与旧的标量乘法操作 <img src="http://latex.codecogs.com/svg.latex?R\times&space;RLWE" title="http://latex.codecogs.com/svg.latex?R\times RLWE" /> 相比，新的标量乘法操作 <img src="http://latex.codecogs.com/svg.latex?\odot:&space;R\times&space;RLWE'\to&space;RLWE" title="http://latex.codecogs.com/svg.latex?\odot: R\times RLWE'\to RLWE" /> 拥有更好的噪声增长控制。</div>


### GSW 密码方案
#### 直觉概念
基于上述的RLWE和RLWE'方法，我们现在介绍(R)GSW。直觉上讲， RGSW构造如下:
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?RGSW_s(m)=(RLWE'_s(-s\cdot&space;m),RLWE'(m))" title="http://latex.codecogs.com/svg.latex?RGSW_s(m)=(RLWE'_s(-s\cdot m),RLWE'(m))" /></p>
<div>定义GSW乘法操作 <img src="http://latex.codecogs.com/svg.latex?\diamond:&space;RLWE\times&space;RGSW\to&space;RLWE" title="http://latex.codecogs.com/svg.latex?\diamond: RLWE\times RGSW\to RLWE" /> 如下</div>
 <p align="center">
  <img src="./fig/GSW.PNG" alt="animated" />
 </p>
<div>注意RGSW除了对m加密，还对sm加密。因此某种意义上说，RGSW可以同态地做解密操作(和bootstrapping概念类似) <img src="http://latex.codecogs.com/svg.latex?RLWE(b-as)=RLWE(qm_0/t&plus;e)=RLWE(\widetilde{m_0}&plus;e)\approx&space;RLWE(\widetilde{m_0})" title="http://latex.codecogs.com/svg.latex?RLWE(b-as)=RLWE(qm_0/t+e)=RLWE(\widetilde{m_0}+e)\approx RLWE(\widetilde{m_0})" />
  
另外注意到为了保证RGSW乘法运算中的噪声足够小，m1一般会取较小值，比如 <img src="http://latex.codecogs.com/svg.latex?m_1=X^v&space;s.t.&space;||e_0m_1||_{\infty}&space;=&space;||e_0||_{\infty}" title="http://latex.codecogs.com/svg.latex?m_1=X^v s.t. ||e_0m_1||_{\infty} = ||e_0||_{\infty}" />。因此最终可得 <img src="http://latex.codecogs.com/svg.latex?RLWE(m_0m_1&plus;e_0m_1)\approx&space;RLWE(m_0m_1)" title="http://latex.codecogs.com/svg.latex?RLWE(m_0m_1+e_0m_1)\approx RLWE(m_0m_1)" /> 。</div>

#### 正式构造（外积，external product）
上述的是GSW的直觉式构造方法，GSW论文给出的实际构造在形式上有一些不同。
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?\begin{align*}RGSW_z(X^m)&space;&=&space;\begin{vmatrix}&space;RLWE_z(0)\\&space;RLWE_z(0)\\&space;\vdots\\&space;RLWE_z(0)\\&space;RLWE_z(0)\\\end{vmatrix}&plus;&space;X^m\cdot&space;\begin{vmatrix}&space;1&space;&&space;0\\&space;0&space;&&space;1\\&space;\vdots\\&space;B_g^{d_g-1}&space;&&space;0\\&space;0&space;&&space;B_g^{d_g-1}\\\end{vmatrix}\\&space;\end{align}" title="http://latex.codecogs.com/svg.latex?\begin{align*}RGSW_z(X^m) &= \begin{vmatrix} RLWE_z(0)\\ RLWE_z(0)\\ \vdots\\ RLWE_z(0)\\ RLWE_z(0)\\\end{vmatrix}+ X^m\cdot \begin{vmatrix} 1 & 0\\ 0 & 1\\ \vdots\\ B_g^{d_g-1} & 0\\ 0 & B_g^{d_g-1}\\\end{vmatrix}\\ \end{align}" /></p>
定义式中的第一个大矩阵(一系列对0加密的LWE密文)和随机矩阵在计算上不可区分，因此RGSW密文在计算上和随机矩阵不可区分，这个特征揭示了RGSW密文的安全性（建立在RLWE密文的安全性之上）。

容易证明下面两个恒等式
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?\begin{align*}RLWE_z(0)&plus;X^m\cdot&space;(0,B_g^{k})&=&space;RLWE_z(X^m\cdot&space;B_g^k)\\RLWE_z(0)&plus;X^m\cdot&space;(B_g^k,0)&space;&=&space;RLWE_z(-z\cdot&space;X^m\cdot&space;B_g^k)\end{align*}&space;" title="http://latex.codecogs.com/svg.latex?\begin{align*}RLWE_z(0)+X^m\cdot (0,B_g^{k})&= RLWE_z(X^m\cdot B_g^k)\\RLWE_z(0)+X^m\cdot (B_g^k,0) &= RLWE_z(-z\cdot X^m\cdot B_g^k)\end{align*} " /></p>

据此，RGSW的定义式可以改写成
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?RGSW_z(X^m)&space;=&space;\begin{vmatrix}&space;RLWE_z(\color{red}{-zX^m})\\&space;RLWE_z(X^m)\\&space;\vdots\\&space;RLWE_z(\color{red}{-zX^mB_g^{d_g-1}})\\&space;RLWE_z(X^mB_g^{d_g-1})\\\end{vmatrix}" title="http://latex.codecogs.com/svg.latex?RGSW_z(X^m) = \begin{vmatrix} RLWE_z(\color{red}{-zX^m})\\ RLWE_z(X^m)\\ \vdots\\ RLWE_z(\color{red}{-zX^mB_g^{d_g-1}})\\ RLWE_z(X^mB_g^{d_g-1})\\\end{vmatrix}" /></p>
<div> 容易看出，上式就是GSW的直觉构造式 <img src="http://latex.codecogs.com/svg.latex?RGSW_z(X^m)=(RLWE'_z(-z\cdot&space;X^m),RLWE'_z(X^m))" title="http://latex.codecogs.com/svg.latex?RGSW_z(X^m)=(RLWE'_z(-z\cdot X^m),RLWE'_z(X^m))" /> 。</div>

接着正式介绍GSW乘法。令一系列RLWE(0)构成的随机矩阵为Z，令Gadget matrix为G，即
<p align="center">
<img src="https://latex.codecogs.com/svg.image?Z&space;=&space;\begin{vmatrix}&space;RLWE_z(0)\\&space;RLWE_z(0)\\&space;\vdots\\&space;RLWE_z(0)\\\end{vmatrix}_{2d_g\times&space;2}G&space;=&space;\begin{vmatrix}&space;1&space;&&space;0\\&space;0&space;&&space;1\\&space;\vdots\\&space;B_g^{d_g-1}&space;&&space;0\\&space;0&space;&&space;B_g^{d_g-1}\\\end{vmatrix}_{2d_g\times&space;2}&space;" title="https://latex.codecogs.com/svg.image?Z = \begin{vmatrix} RLWE_z(0)\\ RLWE_z(0)\\ \vdots\\ RLWE_z(0)\\\end{vmatrix}_{2d_g\times 2}G = \begin{vmatrix} 1 & 0\\ 0 & 1\\ \vdots\\ B_g^{d_g-1} & 0\\ 0 & B_g^{d_g-1}\\\end{vmatrix}_{2d_g\times 2} " />
</p>

定义Gadget decompose操作如下：
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?(a_0,\cdots,a_{d_g-1},b_0,\cdots,b_{d_g-1})\xleftarrow[]{\text{Gadget&space;Decompose}}RLWE(m_0)=(a,b)&space;&space;" title="http://latex.codecogs.com/svg.latex?(a_0,\cdots,a_{d_g-1},b_0,\cdots,b_{d_g-1})\xleftarrow[]{\text{Gadget Decompose}}RLWE(m_0)=(a,b) " />
</p> 使得
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?Gadget\_Decompose(a,b)\cdot&space;G&space;=&space;(a,b)\in&space;R_{n,q}\times&space;R_{n,q}" title="http://latex.codecogs.com/svg.latex?Gadget\_Decompose(a,b)\cdot G = (a,b)\in R_{n,q}\times R_{n,q}" />
</p>

那么可以构造GSW乘法如下所示：
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?\begin{align*}Gadget\_Decompose(RLWE(m_0))\cdot&space;GSW(m_1)&=(a_0,\cdots,a_{d_g-1},b_0,\cdots,b_{d_g-1})\cdot&space;(Z&plus;m_1\cdot&space;G)\\&=&space;RLWE(0)&plus;m_1\cdot&space;RLWE(m_0)\\&=&space;RLWE(0)&plus;m_1\cdot(RLWE(0)&plus;(0,m_0))\\&=&space;RLWE(0)&plus;(0,m_0\cdot&space;m_1)\\&=&space;RLWE(m_0\cdot&space;m_1)\end{align*}" title="http://latex.codecogs.com/svg.latex?\begin{align*}Gadget\_Decompose(RLWE(m_0))\cdot GSW(m_1)&=(a_0,\cdots,a_{d_g-1},b_0,\cdots,b_{d_g-1})\cdot (Z+m_1\cdot G)\\&= RLWE(0)+m_1\cdot RLWE(m_0)\\&= RLWE(0)+m_1\cdot(RLWE(0)+(0,m_0))\\&= RLWE(0)+(0,m_0\cdot m_1)\\&= RLWE(m_0\cdot m_1)\end{align*}" />
</p>

#### 进一步扩展(内积，internal product)
注意到上一节介绍的是非对称形式的GSW乘法，即一个操作数是RLWE，另一个操作数是RGSW。这种形式的乘法最早在TFHE论文中引入，现在称之为external product。那么一个自然的问题是 “是否存在对称形式的GSW乘法，即乘法的两个操作数都是GSW密文呢？”。这个问题最早在GSW论文里讨论，现在通常称之为internal product <img src="http://latex.codecogs.com/svg.latex?\diamond&space;:&space;RGSW\times&space;RGSW\to&space;RGSW" title="http://latex.codecogs.com/svg.latex?\diamond : RGSW\times RGSW\to RGSW" /> 。

为了解决这个问题，首先引入中间状态下的GSW乘法 <img src="http://latex.codecogs.com/svg.latex?\diamond&space;:&space;RLWE'\times&space;RGSW\to&space;RLWE'" title="http://latex.codecogs.com/svg.latex?\diamond : RLWE'\times RGSW\to RLWE'" /> 如下所示
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?\begin{align*}RLWE'(m_0)\diamond&space;RGSW(m_1)&=(RLWE(m_0),\cdots,RLWE(m_0B^{k-1}))\diamond&space;RGSW(m_1)\\&=(RLWE(m_0)\diamond&space;RGSW(m_1),\cdots,&space;RLWE(m_0B^{k-1})\diamond&space;RGSW(m_1))\\&=&space;(RLWE(m_0m_1),\cdots,RLWE(m_0m_1B^{k-1}))\\&=&space;RLWE'(m_0m_1)\end{align*}&space;" title="http://latex.codecogs.com/svg.latex?\begin{align*}RLWE'(m_0)\diamond RGSW(m_1)&=(RLWE(m_0),\cdots,RLWE(m_0B^{k-1}))\diamond RGSW(m_1)\\&=(RLWE(m_0)\diamond RGSW(m_1),\cdots, RLWE(m_0B^{k-1})\diamond RGSW(m_1))\\&= (RLWE(m_0m_1),\cdots,RLWE(m_0m_1B^{k-1}))\\&= RLWE'(m_0m_1)\end{align*} " />
</p>

<div>借助这个中间态，我们最终构造internal product：<img src="http://latex.codecogs.com/svg.latex?\diamond&space;:&space;RGSW\times&space;RGSW\to&space;RGSW" title="http://latex.codecogs.com/svg.latex?\diamond : RGSW\times RGSW\to RGSW" /> </div>

<p align="center">
<img src="http://latex.codecogs.com/svg.latex?\begin{align*}RGSW(m_0)\diamond&space;RGSW(m_1)&=(RLWE'(-sm_0),&space;RLWE'(m_0))\diamond&space;RGSW(m_1)\\&=(RLWE'(-sm_0)\diamond&space;RGSW(m_1),&space;RLWE'(m_0)\diamond&space;RGSW(m_1))\\&=&space;(RLWE'(-sm_0m_1),&space;RLWE'(m_0m_1))\\&=&space;RGSW(m_0m_1)\end{align*}&space;" title="http://latex.codecogs.com/svg.latex?\begin{align*}RGSW(m_0)\diamond RGSW(m_1)&=(RLWE'(-sm_0), RLWE'(m_0))\diamond RGSW(m_1)\\&=(RLWE'(-sm_0)\diamond RGSW(m_1), RLWE'(m_0)\diamond RGSW(m_1))\\&= (RLWE'(-sm_0m_1), RLWE'(m_0m_1))\\&= RGSW(m_0m_1)\end{align*} " />
</p>

#### RGSW的噪声增长分析
这里我们试着分析RGSW乘法操作的噪声变化情况。

首先，讨论内积的噪声变化。注意观察GSW乘法运算的第一项为
<p align="center">
<img src="http://latex.codecogs.com/svg.latex?\begin{align*}(a_0,\cdots,a_{d_g-1},b_0,\cdots,b_{d_g-1})\cdot&space;Z&space;=&space;(a_0,\cdots,a_{d_g-1},b_0,\cdots,b_{d_g-1})\cdot&space;(RLWE(0),\cdots,RLWE(0))^T\\\end{align*}&space;" title="http://latex.codecogs.com/svg.latex?\begin{align*}(a_0,\cdots,a_{d_g-1},b_0,\cdots,b_{d_g-1})\cdot Z = (a_0,\cdots,a_{d_g-1},b_0,\cdots,b_{d_g-1})\cdot (RLWE(0),\cdots,RLWE(0))^T\\\end{align*} " />
</p>
<div>这一项的噪声积累为 <img src="https://latex.codecogs.com/svg.image?\begin{align*}\sum_{i=0}^{d_g-1}a_ie_i&plus;\sum_{i=0}^{d_g-1}b_ie_i'&space;\\\end{align*}&space;" title="https://latex.codecogs.com/svg.image?\begin{align*}\sum_{i=0}^{d_g-1}a_ie_i+\sum_{i=0}^{d_g-1}b_ie_i' \\\end{align*} " />，其中 <img src="https://latex.codecogs.com/svg.image?\{e_i\},\{e_i'\}\gets&space;Error(RGSW(m_1))" title="https://latex.codecogs.com/svg.image?\{e_i\},\{e_i'\}\gets Error(RGSW(m_1))" /> </div>

<div>观察GSW乘法运算的第二项为 <img src="https://latex.codecogs.com/svg.image?\begin{align*}m_1\cdot&space;RLWE(0)&space;\\\end{align*}&space;" title="https://latex.codecogs.com/svg.image?\begin{align*}m_1\cdot RLWE(0) \\\end{align*} " />, 这一部分的噪声积累为 <img src="https://latex.codecogs.com/svg.image?m_1\cdot&space;e&space;" title="https://latex.codecogs.com/svg.image?m_1\cdot e " />, 这里 <img src="https://latex.codecogs.com/svg.image?e=Error(RLWE(m_0))" title="https://latex.codecogs.com/svg.image?e=Error(RLWE(m_0))" /> </div>

综上所述，GSW乘法总的噪声积累表达式是
<p align="center">
<img src="https://latex.codecogs.com/svg.image?Error(RLWE(m_0)\diamond&space;RGSW(m_1))=\sum_{i=0}^{d_g-1}a_ie_i&plus;\sum_{i=0}^{d_g-1}b_ie_i'&space;&plus;&space;m_1e&space;" title="https://latex.codecogs.com/svg.image?Error(RLWE(m_0)\diamond RGSW(m_1))=\sum_{i=0}^{d_g-1}a_ie_i+\sum_{i=0}^{d_g-1}b_ie_i' + m_1e " />
</p>
为了方便分析，我们假定统计独立性，得到噪声方差的上界为
<p align="center">
<img src="https://latex.codecogs.com/svg.image?\inline&space;Var(Error(RGSW(m_0m_1)))\leq&space;2\cdot&space;d_g\cdot&space;n\cdot&space;(\frac{B_g}{2})^2\cdot&space;Var(e_i)&plus;&space;||m_1||_2\cdot&space;Var(e)" title="https://latex.codecogs.com/svg.image?\inline Var(Error(RGSW(m_0m_1)))\leq 2\cdot d_g\cdot n\cdot (\frac{B_g}{2})^2\cdot Var(e_i)+ ||m_1||_2\cdot Var(e)" /></p>

同理可以分析internal product中的噪声，结论和external product是一致的。通过分析GSW乘法的噪声，可以看出GSW控制噪声最核心的部件是Gadget_Decompose。Gadget_Decompose将RLWE(m0)分解成小系数形式的 <img src="https://latex.codecogs.com/svg.image?\{a_i\},\{b_i\}" title="https://latex.codecogs.com/svg.image?\{a_i\},\{b_i\}" />, 这些小系数与矩阵Z作用形成RLWE(0)。正因为 <img src="https://latex.codecogs.com/svg.image?\{a_i\},\{b_i\}" title="https://latex.codecogs.com/svg.image?\{a_i\},\{b_i\}" /> 幅值小，所以形成的Error(RLWE(0))也较小，这样就顺利抑制住噪声增长。
