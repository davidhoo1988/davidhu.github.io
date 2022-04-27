# 基于Oblivious Transfer构造PSI

## 预备知识 Oblivious Transfer
Oblivious Transfer (OT,透明传输) 是一种特殊的多方安全计算。OT设定存在计算双方Alice和Bob，Alice拥有一个选择比特 <img src="https://latex.codecogs.com/svg.image?c\in\{0,1\}" title="https://latex.codecogs.com/svg.image?c\in\{0,1\}" /> , Bob拥有2个字符串 <img src="https://latex.codecogs.com/svg.image?s_0" title="https://latex.codecogs.com/svg.image?s_0" /> 和 <img src="https://latex.codecogs.com/svg.image?s_1" title="https://latex.codecogs.com/svg.image?s_1" /> 。Bob需要根据Alice的选择比特 <img src="https://latex.codecogs.com/svg.image?c" title="https://latex.codecogs.com/svg.image?c" /> “秘密”地传输其中一个字符串，即 <img src="https://latex.codecogs.com/svg.image?s_c" title="https://latex.codecogs.com/svg.image?s_c" /> ， 给Alice。传输过程需要保证Alice只了解 <img src="https://latex.codecogs.com/svg.image?s_c" title="https://latex.codecogs.com/svg.image?s_c" /> 而不了解 <img src="https://latex.codecogs.com/svg.image?s_{1-c}" title="https://latex.codecogs.com/svg.image?s_{1-c}" /> ；并且Bob不了解Alice拥有的 <img src="https://latex.codecogs.com/svg.image?c" title="https://latex.codecogs.com/svg.image?c" /> 。

下图描述了整个思路：
   <p align="center">
  <img src="fig/OT.png" alt="animated" />
   </p>
   
## (Oblivious) Equality Test
这里的问题是这样的：Alice拥有N-bit 字符串 <img src="https://latex.codecogs.com/svg.image?x=\{x_i\}_{i=0,\cdots,N-1}" title="https://latex.codecogs.com/svg.image?x=\{x_i\}_{i=0,\cdots,N-1}" /> ，Bob有N-bit 字符串 <img src="https://latex.codecogs.com/svg.image?y=\{y_i\}_{i=0,\cdots,N-1}" title="https://latex.codecogs.com/svg.image?y=\{y_i\}_{i=0,\cdots,N-1}" />，现在比较x是否等于y，但是不泄露x，y本身(也就是说，当x不等于y时，Alice不能获取关于y的任何信息，Bob也不能获得关于x的任何信息)。

利用OT我们可以这样构造Equality Test：Bob给自己私有字符串y的每一个比特分配两个随机数（每个随机数有 <img src="https://latex.codecogs.com/svg.image?\lambda" title="https://latex.codecogs.com/svg.image?\lambda" /> 比特），分别对应‘0’和‘1’，记为 <img src="https://latex.codecogs.com/svg.image?\{S_{i,0},S_{i,1}\}_{i=0,\cdots,N-1}" title="https://latex.codecogs.com/svg.image?\{S_{i,0},S_{i,1}\}_{i=0,\cdots,N-1}" /> 。接着Alice按照自己私有字符串x的比特数值为索引，和Bob进行N次1-out-of-2 OT，获得Bob手中的N个随机数，即 <img src="https://latex.codecogs.com/svg.image?\{S_{i,x_i}\}_{i=0,\cdots,N-1}" title="https://latex.codecogs.com/svg.image?\{S_{i,x_i}\}_{i=0,\cdots,N-1}" /> 。最后Bob计算 <img src="https://latex.codecogs.com/svg.image?\oplus_i&space;S_{i,y_i}&space;" title="https://latex.codecogs.com/svg.image?\oplus_i S_{i,y_i} " /> 并发送给Alice，Alice计算 <img src="https://latex.codecogs.com/svg.image?\oplus_i&space;S_{i,x_i}&space;" title="https://latex.codecogs.com/svg.image?\oplus_i S_{i,x_i} " /> 并和 <img src="https://latex.codecogs.com/svg.image?\oplus_i&space;S_{i,y_i}&space;" title="https://latex.codecogs.com/svg.image?\oplus_i S_{i,y_i} " /> 比较，如果两者相当，则说明 <img src="https://latex.codecogs.com/svg.image?x&space;=&space;y" title="https://latex.codecogs.com/svg.image?x = y" /> , 否则 <img src="https://latex.codecogs.com/svg.image?x\neq&space;y" title="https://latex.codecogs.com/svg.image?x\neq y" /> 。

下图描述了整个思路：
   <p align="center">
  <img src="fig/EqualityTest.png" alt="animated" />
   </p>
   
## Membership Test
有了上述Equality Test的构造，容易进一步构造Membership Test: Alice拥有N-bit 字符串 <img src="https://latex.codecogs.com/svg.image?x=\{x_i\}_{i=0,\cdots,N-1}" title="https://latex.codecogs.com/svg.image?x=\{x_i\}_{i=0,\cdots,N-1}" /> ，Bob 拥有 <img src="https://latex.codecogs.com/svg.image?\ell" title="https://latex.codecogs.com/svg.image?\ell" /> 个N-bit字符串 <img src="https://latex.codecogs.com/svg.image?Y=\{y_0,\cdots,y_{\ell-1}\}&space;\text{&space;with&space;}&space;y_i=\{y_{i,j}\}_{i=0,\cdots,\ell-1;j=0,\cdots,N-1}" title="https://latex.codecogs.com/svg.image?Y=\{y_0,\cdots,y_{\ell-1}\} \text{ with } y_i=\{y_{i,j}\}_{i=0,\cdots,\ell-1;j=0,\cdots,N-1}" /> 。现在要求判断 <img src="https://latex.codecogs.com/svg.image?x\in&space;Y" title="https://latex.codecogs.com/svg.image?x\in Y" /> 且不泄露 <img src="https://latex.codecogs.com/svg.image?x,&space;Y" title="https://latex.codecogs.com/svg.image?x, Y" /> 。

具体构造如下：
1. 对所有的 <img src="https://latex.codecogs.com/svg.image?y_i" title="https://latex.codecogs.com/svg.image?y_i" /> 都做一遍Equality Test，这样共计做 <img src="https://latex.codecogs.com/svg.image?\lambda" title="https://latex.codecogs.com/svg.image?\lambda" /> 次Equality Test。
2. 若发现这 <img src="https://latex.codecogs.com/svg.image?\lambda" title="https://latex.codecogs.com/svg.image?\lambda" /> 次Equality Test出现元素相等的情况，说明 <img src="https://latex.codecogs.com/svg.image?x\in&space;Y" title="https://latex.codecogs.com/svg.image?x\in Y" /> ； 否则说明 <img src="https://latex.codecogs.com/svg.image?x\notin&space;Y" title="https://latex.codecogs.com/svg.image?x\notin Y" /> 。

## Set Intersection
最后介绍求交运算 (Private Set Intersection)。这里设定Alice拥有集合 <img src="https://latex.codecogs.com/svg.image?X&space;=&space;\{x_0,\cdots,x_{n-1}\}" title="https://latex.codecogs.com/svg.image?X = \{x_0,\cdots,x_{n-1}\}" />， Bob拥有集合 <img src="https://latex.codecogs.com/svg.image?Y&space;=&space;\{y_0,\cdots,y_{n-1}\}" title="https://latex.codecogs.com/svg.image?Y = \{y_0,\cdots,y_{n-1}\}" />； 现在要求Alice和Bob协同计算 <img src="https://latex.codecogs.com/svg.image?X\cap&space;Y" title="https://latex.codecogs.com/svg.image?X\cap Y" /> 但不向对方泄露自己的集合。

利用上述Membership Test容易构造Private Set Intersection如下：
1. 对Alice的集合X中的每一个元素做一次Membership Test, 共计做n次Membership Test （等价于做 <img src="https://latex.codecogs.com/svg.image?n^2" title="https://latex.codecogs.com/svg.image?n^2" /> 次Equality Test）。
2. n次Membership Test返回n次判断结果，即可得到Alice的集合X中从属集合Y中的元素，即 <img src="https://latex.codecogs.com/svg.image?X\cap&space;Y" title="https://latex.codecogs.com/svg.image?X\cap Y" /> 。

## 进一步优化
注意到上面描述的Private Set Intersection算法需要进行 <img src="https://latex.codecogs.com/svg.image?n^2" title="https://latex.codecogs.com/svg.image?n^2" /> 次比较(Equality Test)。 如果集合很大时，比较数量是巨大的。那么问题来了，有没有可能减少PSI当中的比较次数呢？ 答案是利用哈希(Hashing)。

使用Hash，可以将比较次数降至 <img src="https://latex.codecogs.com/svg.image?\mathcal{O}(nlogn)" title="https://latex.codecogs.com/svg.image?\mathcal{O}(nlogn)" /> 。


