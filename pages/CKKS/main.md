# CKKS方案
CKKS区别于BFV最突出的地方就在于SIMD编码。BFV利用SIMD加密的是一个整型向量，而CKKS利用SIMD加密的是一个浮点型向量(更精确地说是复数型向量)

## SIMD编码
CKKS SIMD编解码过程可以用下面表示:

<p align="center">
  <img src="fig/simd.png" alt="animated"/>
</p>

