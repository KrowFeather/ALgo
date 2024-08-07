## 题目：
给你三个整数 $a,b,n$ 范围 $(a\oplus x)\times(b\oplus x)$ 的最大值，且 $x$ 需要满足 $0\leq x\leq 2^n$。

## 题解：
对于这一类题目，如果我们直接枚举我们的 x，我们一定拿到的不是我们的最大值，我们对于异或运算，我们最好还是有限考虑我们的贪心，
我们按照 a 和 b 再这一位上的取值来分类：
$a=0,b=0$,构造 $1$, $a=1,b=1$,构造 $0$。
那么在我们两个异或为 1 的条件下，我们应该怎么构造？这里，我们注意到，我们无论怎么构造，我们这一位的和都是相等的，于是，我们就可以根据均值不等式，让我们两者之间的差尽可能的小。
![[Pasted image 20240304153406.png]]

最后就是对于我们的限制，我们只用关心我们的最高位是不是填的 1，如果是，我们直接结束了。否则，我们剩下的一定都可以填，我们就按照我们上述的来填写即可。

```cpp
int maximumXorProduct(long long a, long long b, int n) {
    long long x = 1L << n, a_, b_; int mod = 1000000007;
    while (x >>= 1) {
        a_ = a & x; b_ = b & x;
        if      (a_ ==  0 && b_ == 0) { a |=  x; b |=  x; }
        else if (a_ != b_ && a  >  b) { a &= ~x; b |=  x; }
        else if (a_ != b_ && a  <= b) { a |=  x; b &= ~x; }
    }
    return (a % mod) * (b % mod) % mod;
}
```