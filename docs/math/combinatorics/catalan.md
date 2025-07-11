## Catalan 数列

Catalan 数列 $H_n$ 可以应用于以下问题：

1.  有 $2n$ 个人排成一行进入剧场。入场费 5 元。其中只有 $n$ 个人有一张 5 元钞票，另外 $n$ 人只有 10 元钞票，剧院无其它钞票，问有多少种方法使得只要有 10 元的人买票，售票处就有 5 元的钞票找零？
2.  有一个大小为 $n\times n$ 的方格图左下角为 $(0, 0)$ 右上角为 $(n, n)$，从左下角开始每次都只能向右或者向上走一单位，不走到对角线 $y=x$ 上方（但可以触碰）的情况下到达右上角有多少可能的路径？
3.  在圆上选择 $2n$ 个点，将这些点成对连接起来使得所得到的 $n$ 条线段不相交的方法数？
4.  对角线不相交的情况下，将一个凸多边形区域分成三角形区域的方法数？
5.  一个栈（无穷大）的进栈序列为 $1,2,3, \cdots ,n$ 有多少个不同的出栈序列？
6.  $n$ 个结点可构造多少个不同的二叉树？
7.  由 $n$ 个 $+1$ 和 $n$ 个 $-1$ 组成的 $2n$ 个数 $a_1,a_2, \cdots ,a_{2n}$，其部分和满足 $a_1+a_2+ \cdots +a_k \geqslant 0~(k=1,2,3, \cdots ,2n)$，有多少个满足条件的数列？

其对应的序列为：

| $H_0$ | $H_1$ | $H_2$ | $H_3$ | $H_4$ | $H_5$ | $H_6$ | ... |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :-: |
|   1   |   1   |   2   |   5   |   14  |   42  |  132  | ... |

## 递推式

该递推关系的解为：

$$
H_n = \frac{\binom{2n}{n}}{n+1}(n \geqslant 2, n \in \mathbf{N_{+}})
$$

关于 Catalan 数的常见公式：

$$
H_n = \begin{cases}
    \sum_{i=1}^{n} H_{i-1} H_{n-i} & n \geqslant 2, n \in \mathbf{N_{+}}\\
    1 & n = 0, 1
\end{cases}
$$

$$
H_n = \frac{H_{n-1} (4n-2)}{n+1}
$$

$$
H_n = \binom{2n}{n} - \binom{2n}{n-1}
$$

??? note " 例题 [洛谷 P1044 栈](https://www.luogu.com.cn/problem/P1044)"
    题目大意：入栈顺序为 $1,2,\ldots ,n$，求所有可能的出栈顺序的总数。

=== "C++"
    ```cpp
    #include <iostream>
    using namespace std;
    int n;
    long long f[25];
    
    int main() {
      f[0] = 1;
      cin >> n;
      for (int i = 1; i <= n; i++) f[i] = f[i - 1] * (4 * i - 2) / (i + 1);
      // 这里用的是常见公式2
      cout << f[n] << endl;
      return 0;
    }
    ```

=== "Python"
    ```python
    f = [0] * 25
    f[0] = 1
    n = int(input())
    for i in range(1, n + 1):
        f[i] = int(f[i - 1] * (4 * i - 2) // (i + 1))
        # 这里用的是常见公式2
    print(f[n])
    ```

## 封闭形式

卡特兰数的递推式为

$$
H_n=\sum_{i=0}^{n-1}H_{i}H_{n-i-1} \quad (n\geqslant 2)
$$

其中 $H_0=1,H_1=1$。设它的普通生成函数为 $H(x)$。

我们发现卡特兰数的递推式与卷积的形式很相似，因此我们用卷积来构造关于 $H(x)$ 的方程：

$$
\begin{aligned}
H(x)&=\sum_{n\geqslant 0}H_nx^n\\
&=1+\sum_{n\geqslant 1}\sum_{i=0}^{n-1}H_ix^iH_{n-i-1}x^{n-i-1}x\\
&=1+x\sum_{i\geqslant 0}H_{i}x^i\sum_{n\geqslant 0}H_{n}x^{n}\\
&=1+xH^2(x)
\end{aligned}
$$

解得

$$
H(x)=\frac{1\pm \sqrt{1-4x}}{2x}
$$

那么这就产生了一个问题：我们应该取哪一个根呢？我们将其分子有理化：

$$
H(x)=\frac{2}{1\mp \sqrt{1-4x}}
$$

代入 $x=0$，我们得到的是 $H(x)$ 的常数项，也就是 $H_0$。当 $H(x)=\dfrac{2}{1+\sqrt{1-4x}}$ 的时候有 $H(0)=1$，满足要求。而另一个解会出现分母为 $0$ 的情况（不收敛），舍弃。

因此我们得到了卡特兰数生成函数的封闭形式：

$$
H(x)=\frac{1- \sqrt{1-4x}}{2x}
$$

接下来我们要将其展开。但注意到它的分母不是斐波那契数列那样的多项式形式，因此不方便套用等比数列的展开形式。在这里我们需要使用牛顿二项式定理。我们来先展开 $\sqrt{1-4x}$：

$$
\begin{aligned}
(1-4x)^{\frac{1}{2}}
&=\sum_{n\geqslant 0}\binom{\frac{1}{2}}{n}(-4x)^n\\
&=1+\sum_{n\geqslant 1}\frac{\left(\frac{1}{2}\right)^{\underline{n}}}{n!}(-4x)^n
\end{aligned} \tag{1}
$$

注意到

$$
\begin{aligned}
\left(\frac{1}{2}\right)^{\underline{n}}
&=\frac{1}{2}\frac{-1}{2}\frac{-3}{2}\cdots\frac{-(2n-3)}{2}\\
&=\frac{(-1)^{n-1}(2n-3)!!}{2^n}\\
&=\frac{(-1)^{n-1}(2n-2)!}{2^n(2n-2)!!}\\
&=\frac{(-1)^{n-1}(2n-2)!}{2^{2n-1}(n-1)!}
\end{aligned}
$$

这里使用了双阶乘的化简技巧。那么带回 $(1)$ 得到

$$
\begin{aligned}
(1-4x)^{\frac{1}{2}}
&=1+\sum_{n\geqslant 1}\frac{(-1)^{n-1}(2n-2)!}{2^{2n-1}(n-1)!n!}(-4x)^n\\
&=1-\sum_{n\geqslant 1}\frac{(2n-2)!}{(n-1)!n!}2x^n\\
&=1-\sum_{n\geqslant 1}\binom{2n-1}{n}\frac{1}{(2n-1)}2x^n
\end{aligned}
$$

带回原式得到

$$
\begin{aligned}
H(x)&=\frac{1- \sqrt{1-4x}}{2x}\\
&=\frac{1}{2x}\sum_{n\geqslant 1}\binom{2n-1}{n}\frac{1}{(2n-1)}2x^n\\
&=\sum_{n\geqslant 1}\binom{2n-1}{n}\frac{1}{(2n-1)}x^{n-1}\\
&=\sum_{n\geqslant 0}\binom{2n+1}{n+1}\frac{1}{(2n+1)}x^{n}\\
&=\sum_{n\geqslant 0}\binom{2n}{n}\frac{1}{n+1}x^{n}\\
\end{aligned}
$$

这样我们就得到了卡特兰数的通项公式。

## 路径计数问题

非降路径是指只能向上或向右走的路径。

1.  从 $(0,0)$ 到 $(m,n)$ 的非降路径数等于 $m$ 个 $x$ 和 $n$ 个 $y$ 的排列数，即 $\dbinom{n + m}{m}$。

2.  从 $(0,0)$ 到 $(n,n)$ 的除端点外不接触直线 $y=x$ 的非降路径数：

    先考虑 $y=x$ 下方的路径，都是从 $(0, 0)$ 出发，经过 $(1, 0)$ 及 $(n, n-1)$ 到 $(n,n)$，可以看做是 $(1,0)$ 到 $(n,n-1)$ 不接触 $y=x$ 的非降路径数。

    所有的的非降路径有 $\dbinom{2n-2}{n-1}$ 条。对于这里面任意一条接触了 $y=x$ 的路径，可以把它最后离开这条线的点到 $(1,0)$ 之间的部分关于 $y=x$ 对称变换，就得到从 $(0,1)$ 到 $(n,n-1)$ 的一条非降路径。反之也成立。从而 $y=x$ 下方的非降路径数是 $\dbinom{2n-2}{n-1} - \dbinom{2n-2}{n}$。根据对称性可知所求答案为 $2\dbinom{2n-2}{n-1} - 2\dbinom{2n-2}{n}$。

3.  从 $(0,0)$ 到 $(n,n)$ 的除端点外不穿过直线 $y=x$ 的非降路径数：

    用类似的方法可以得到：$\dfrac{2}{n+1}\dbinom{2n}{n}$
