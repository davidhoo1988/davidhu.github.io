# 基于Oblivious Transfer构造PSI

## (Oblivious) Equality Test
这里的问题是这样的：Alice有N-bit 字符串x，Bob有N-bit 字符串y，现在比较x是否等于y，但是不泄露x，y本身(也就是说，当x不等于y时，Alice不能获取关于y的任何信息，Bob也不能获得关于x的任何信息)。

利用OT我们可以这样构造Equality Test
下图描述了整个思路：

### Membership Test
