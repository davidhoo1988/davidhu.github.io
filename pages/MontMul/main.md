# 蒙哥马利模乘

蒙哥马利模乘最主要的贡献就是提供了一种给定输入$T$，快速计算$TR^{-1}\bmod N (R>N)$的模约减方法。为了描述方便将该模约减方法记为$TR^{-1}\gets REDC(T)$。
现在定义一个整数$a\in \mathbb{Z}_{N}$
的蒙哥马利形式(Montgomery form, Montgomery domain)为$aR\in \mathbb{Z}_N$。显然，一个整数$a$的蒙哥马利形式可以先算一次乘法$a\cdot R^2$ 最后算一次REDC得到，即:
$$aR = REDC(aR^2)$$

现在考虑如何计算两个整数$a$和$b$的模乘，即$ab\bmod N$。这里利用蒙哥马利模乘达成这个计算目标。蒙哥马利模乘可以分三步进行计算。
1. 将输入转成蒙哥马利形式，即 $aR=REDC(aR^2), bR=REDC(bR^2)$
2. 做一次标准乘法得$abR^2=aR\cdot bR$
3. 最后做一次REDC得 $abR=REDC(abR^2)$

注意上面三个步骤返回的是蒙哥马利形式的$ab$，即$abR$。若需要转换成正常形式的$ab$，需要再做一次REDC得$ab=REDC(abR)$。


## 蒙哥马利模约减
现在讨论蒙哥马利模乘最重要的部分REDC。下面给出REDC的算法描述

```python
function REDC is
    input: Integers R and N with gcd(R, N) = 1,
           Integer N′ in [0, R − 1] such that NN′ ≡ −1 mod R,
           Integer T in the range [0, RN − 1].
    output: Integer S in the range [0, N − 1] such that S ≡ TR−1 mod N

    m ← ((T mod R)N′) mod R
    t ← (T + mN) / R
    if t ≥ N then
        return t − N
    else
        return t
    end if
end function
```

一般地，想做模约减运算，需要做一次试除，即 $a \bmod N = a-N\cdot \lfloor a/N\rfloor$。 REDC的核心思想是避免做除法$\lfloor a/N\rfloor$,但是仍能求得模约减的结果。
作为代价, REDC算的模约减结果包含了一个'尾巴' $R^{-1}$。这也是为什么蒙哥马利模乘算法当中反复强调要在蒙哥马利形式下进行的根本原因。

接着，我们讨论构造REDC算法当中的直觉。注意REDC算法的输入是T。 我们希望T加上N的某个整数倍恰好能被R整除，即 $(T+mN)/R$ 是一个整数。通过巧妙地选择参数$m$使得 $(T+mN)/R$ 是一个比较小（小于2N）的整数, 且 $(T+mN)/R = TR^{-1} \bmod N$。


最后我们正式地证明REDC算法的正确性。容易分析出证明REDC是正确的，只需证明下面三个性质成立
1. $T+mN$ 被R整除。  证明: $T+mN \equiv T + ||T|_RN'|_R\cdot N\equiv T+TN'N \equiv T-T \equiv 0 \bmod R$
2. $t\equiv TR^{-1} \bmod N$。证明: $t\equiv (T+mN)R^{-1} \equiv TR^{-1}+mR^{-1}N\equiv TR^{-1} (\bmod N)$
3. $(T+mN)/R<2N$。 证明: 易知$m\leq R-1$, $T\leq RN-1$, 所以 $T+mN\leq 2RN-N-1<2RN$

## 代码实现
最后给出一个蒙哥马利模乘的Python实现以供参考。注意程序里面取$R=2^{64}$, 所以 $\bmod R$相当于取低64bit；$/R$相当于右移64bit。

<details><summary>展开代码</summary>
<p>
    
```python
import math

class MontMul:
    """docstring for ClassName"""
    def __init__(self, R, N):
        self.N = N
        self.R = R
        self.logR = int(math.log(R, 2))
        N_inv = MontMul.modinv(N, R)
        self.N_inv_neg = R - N_inv
        self.R2 = (R*R)%N

    @staticmethod        
    def egcd(a, b):
        if a == 0:
            return (b, 0, 1)
        else:
            g, y, x = MontMul.egcd(b % a, a)
            return (g, x - (b // a) * y, y)

    @staticmethod
    def modinv(a, m):
        g, x, y = MontMul.egcd(a, m)
        if g != 1:
            raise Exception('modular inverse does not exist')
        else:
            return x % m

    def REDC(self, T):
        N, R, logR, N_inv_neg = self.N, self.R, self.logR, self.N_inv_neg

        m = ((T&int('1'*logR, 2)) * N_inv_neg)&int('1'*logR, 2) # m = (T%R * N_inv_neg)%R        
        t = (T+m*N) >> logR # t = int((T+m*N)/R)
        if t >= N:
            return t-N
        else:
            return t

    def ModMul(self, a, b):
        if a >= self.N or b >= self.N:
            raise Exception('input integer must be smaller than the modulus N')

        R2 = self.R2
        aR = self.REDC(a*R2) # convert a to Montgomery form
        bR = self.REDC(b*R2) # convert b to Montgomery form
        T = aR*bR # standard multiplication
        abR = self.REDC(T) # Montgomery reduction
        return self.REDC(abR) # covnert abR to normal ab

if __name__ == '__main__':
    N = 123456789
    R = 2**64 # assume here we are working on 64-bit integer multiplication
    g, x, y = MontMul.egcd(N,R)
    if R<=N or g !=1: 
        raise Exception('N must be larger than R and gcd(N,R) == 1')
    inst = MontMul(R, N)

    input1, input2 = 23456789, 12345678
    mul = inst.ModMul(input1, input2)
    if mul == (input1*input2)%N:
        print ('({input1}*{input2})%{N} is {mul}'.format(input1 = input1, input2 = input2, N = N, mul = mul))
```
</p>
    
</details>
