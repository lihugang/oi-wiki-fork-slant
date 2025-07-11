前置知识：[分块](../ds/decompose.md)。

朴素的打表，指的是在比赛时把所有可能的输入对应的答案都计算出来并保存下来，然后在代码里开个数组把答案放里面，直接输出即可。

注意这个技巧只适用于输入的值域不大（如，输入只有一个数，而且范围很小）的问题，否则可能会导致代码过长、MLE、打表需要的时间过长等问题。

???+ note "例题"
    规定 $f(x)$ 为整数 $x$ 的二进制表示中 $1$ 的个数。输入一个正整数 $n$($n\leqslant 10^9$)，输出 $\sum_{i=1}^n f^2(i)$。

如果对于每一个 $n$，都输出 $f(n)$ 的话，除了可能会 MLE 外，还有可能代码超过最大代码长度限制，导致编译不通过。

我们考虑优化这个答案表。采用 [分块](../ds/decompose.md) 的思想，我们设置一个合理的步长 $m$（这个步长一般视代码长度而定），对于第 $i$ 块，计算出：

$$
\sum_{k=\frac{n}{m}(i-1)+1}^{\frac{ni}{m}} f^2(k)
$$

的值。

然后输出答案时采用分块思想处理即可。即，整块的答案用预处理的值计算，非整块的答案暴力计算。

一般来说，这样的问题对于处理单个函数值 $f(x)$ 很快，但是需要大量函数值求和（求积或某些可以快速合并的操作），枚举会超出时间限制，在找不到标准做法的情况下，分段打表是一个不错的选择。

???+ note "注意事项"
    当上题中指数不是定值，但是范围较小，也可以考虑打表。

### 例题

[「BZOJ 3798」特殊的质数](https://hydro.ac/p/bzoj-P3798)：求 $[l,r]$ 区间内有多少个质数可以分解为两个正整数的平方和。

[「Luogu P1822」魔法指纹](https://www.luogu.com.cn/problem/P1822)
