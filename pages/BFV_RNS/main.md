# RNS版本的BFV方案

这篇博文主要基于20116年的BEHZ论文。目前所有实用的第二代FHE方案都是在RNS(Residue Number System, or Chinese Remainder Theorem Representation)上做的。这是因为RNS具有很高的计算并行度，并且可以利用计算机自带的整数类型(int32, int64)直接支持FHE方案需要的大数（$\mathbf{F}_{p}, \text{p is at least hundreds of bits long}$)
在正常数制下的BFV的介绍可以参考我之前的一篇博文。
