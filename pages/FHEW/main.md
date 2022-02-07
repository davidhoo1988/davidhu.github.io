## FHEW介绍

FHEW是开启第三代FHE的标志性方案。该方案主要是Leo Ducas he Daniele Micciancio在2014年提出并设计。原始论文可以参考[这里](https://eprint.iacr.org/2014/816.pdf)。与第二代FHE方案相比，bootstrapping的性能得到大幅度提升，在常见的台式机平台上速度可以达到毫秒级别；但同时因为缺少第二代FHE的SIMD特性，FHEW只能处理若干比特的加法和乘法操作，也就是说同态乘法的性能较差。

### 预备知识
#### Learning With Errors (LWE)
![equation](https://latex.codecogs.com/svg.image?LWE(m)=(\vec{a},b))
