数论分块可以快速计算一些含有除法向下取整的和式（即形如 $\sum_{i=1}^nf(i)g(\left\lfloor\dfrac ni\right\rfloor)$ 的和式）。当可以在 $O(1)$ 内计算 $f(r)-f(l)$ 或已经预处理出 $f$ 的前缀和时，数论分块就可以在 $O(\sqrt n)$ 的时间内计算上述和式的值。

它主要利用了富比尼定理（Fubini's theorem），将 $\left\lfloor\dfrac ni\right\rfloor$ 相同的数打包同时计算。

???+ note "富比尼定理"
    又称「算两次」，以意大利数学家圭多·富比尼（Guido Fubini）命名。
    富比尼定理的积分形式：只要二重积分 $\int\int |f(x,y)|dxdy$ 有界，则可以逐次计算二重积分，并且可以交换逐次积分的顺序。
    积分号也是特殊的求和号，因此在一般求和中，富比尼定理往往呈现为更换计数顺序，即交换两个求和号。
    组合数学中的富比尼定理表现为，用两种不同的方法计算同一个量，从而建立相等关系。

例如这里的双曲线下整点的图片：

![双曲线下整点](./images/sqrt-decomposition.svg)

图中共分为了 $5$ 块，这 $5$ 块整点的最大纵坐标都相同。如果统计整点的个数，可以从纵向计数改为横向计数，直接计算 $5$ 个矩形即可。

## 引理 1

$$
\forall a,b,c\in\mathbb{Z},\left\lfloor\frac{a}{bc}\right\rfloor=\left\lfloor\frac{\left\lfloor\frac{a}{b}\right\rfloor}{c}\right\rfloor
$$

略证：

$$
\begin{aligned}
&\frac{a}{b}=\left\lfloor\frac{a}{b}\right\rfloor+r(0\leqslant r<1)\\
\implies
&\left\lfloor\frac{a}{bc}\right\rfloor
=\left\lfloor\frac{a}{b}\cdot\frac{1}{c}\right\rfloor
=\left\lfloor \frac{1}{c}\left(\left\lfloor\frac{a}{b}\right\rfloor+r\right)\right\rfloor
=\left\lfloor \frac{\left\lfloor\frac{a}{b}\right\rfloor}{c} +\frac{r}{c}\right\rfloor
=\left\lfloor \frac{\left\lfloor\frac{a}{b}\right\rfloor}{c}\right\rfloor\\
&&\square
\end{aligned}
$$

??? note "关于证明最后的小方块"
    QED 是拉丁词组「Quod Erat Demonstrandum」（这就是所要证明的）的缩写，代表证明完毕。现在的 QED 符号通常是 $\blacksquare$ 或者 $\square$。（[维基百科](https://en.wikipedia.org/wiki/Q.E.D.)）

## 引理 2

$$
\forall n \in \mathbb{N}_{+},  \left|\left\{ \lfloor \frac{n}{d} \rfloor \mid d \in \mathbb{N}_{+},d\leqslant n \right\}\right| \leqslant \lfloor 2\sqrt{n} \rfloor
$$

$|V|$ 表示集合 $V$ 的元素个数

略证：

对于 $d\leqslant \left\lfloor\sqrt{n}\right\rfloor$，$\left\lfloor\frac{n}{d}\right\rfloor$ 有 $\left\lfloor\sqrt{n}\right\rfloor$ 种取值

对于 $d> \left\lfloor\sqrt{n}\right\rfloor$，有 $\left\lfloor\frac{n}{d}\right\rfloor\leqslant\left\lfloor\sqrt{n}\right\rfloor$，也只有 $\left\lfloor\sqrt{n}\right\rfloor$ 种取值

综上，得证

## 数论分块结论

对于常数 $n$，使得式子

$$
\left\lfloor\dfrac ni\right\rfloor=\left\lfloor\dfrac nj\right\rfloor
$$

成立且满足 $i\leqslant j\leqslant n$ 的 $j$ 值最大为 $\left\lfloor\dfrac n{\lfloor\frac ni\rfloor}\right\rfloor$，即值 $\left\lfloor\dfrac ni\right\rfloor$ 所在块的右端点为 $\left\lfloor\dfrac n{\lfloor\frac ni\rfloor}\right\rfloor$。

??? note "证明"
    令 $k=\left\lfloor\dfrac ni\right\rfloor$，可以知道 $k\leqslant\dfrac ni$。
    
    $$
    \begin{aligned}
    &\therefore \left\lfloor\dfrac nk\right\rfloor\geqslant\left\lfloor\dfrac n{\frac ni}\right\rfloor=\lfloor i\rfloor=i\\
    &\therefore j=\max{\text{满足条件的所有 }i}=i_{\max}=\left\lfloor\dfrac nk\right\rfloor=\left\lfloor\dfrac n{\left\lfloor\dfrac ni\right\rfloor}\right\rfloor \square
    \end{aligned}
    $$

## 过程

数论分块的过程大概如下：考虑和式

$\sum_{i=1}^nf(i)\left\lfloor\dfrac ni\right\rfloor$

那么由于我们可以知道 $\left\lfloor\dfrac ni\right\rfloor$ 的值成一个块状分布（就是同样的值都聚集在连续的块中），那么就可以用数论分块加速计算，降低时间复杂度。

利用上述结论，我们先求出 $f(i)$ 的 **前缀和**（记作 $s(i)=\sum_{j=1}^i f(j)$），然后每次以 $[l,r]=[l,\left\lfloor\dfrac n{\lfloor\frac ni\rfloor}\right\rfloor]$ 为一块，分块求出贡献累加到结果中即可。

伪代码如下：

$$
\begin{array}{ll}
1 & \text{Calculate $s(i)$, the prefix sum of $f(i)$.} \\
2 & l \gets 1\\
3 & r \gets 0\\
4 & \textit{result} \gets 0 \\
5 & \textbf{while } l \leqslant n \textbf{ do} : \\
6 & \qquad r \gets \left\lfloor \dfrac{n}{\lfloor n/l \rfloor} \right\rfloor\\
7 & \qquad \textit{result} \gets \textit{result} + [s(r)-s(l-1)] \times\left\lfloor \dfrac{n}{l} \right\rfloor\\
8 & \qquad l \gets r+1\\
9 & \textbf{end while }\\
\end{array}
$$

最终得到的 $result$ 即为所求的和式。

???+ note " 例题：[UVa11526 H(n)](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=27&page=show_problem&problem=2521)"
    题意：$T$ 组数据，每组一个整数 $n$。对于每组数据，输出 $\sum_{i=1}^n\left\lfloor\dfrac ni\right\rfloor$。
    
    ??? note "思路"
        如上推导，对于每一块相同的 $\left\lfloor\dfrac ni\right\rfloor$ 一起计算。时间复杂度为 $O(T\sqrt n)$。
    
    ??? note "实现"
        ```cpp
        --8<-- "docs/math/code/sqrt-decomposition/sqrt-decomposition_1.cpp"
        ```

## 向上取整的数论分块

向上取整与前文所述的向下取整十分类似，但略有区别：

对于常数 $n$，使得式子

$$
\left\lceil\dfrac ni\right\rceil=\left\lceil\dfrac nj\right\rceil
$$

成立且满足 $i\leqslant j\leqslant n$ 的 $j$ 值最大为 $\left\lfloor\dfrac{n-1}{\lfloor\frac{n-1}i\rfloor}\right\rfloor$，即值 $\left\lceil\dfrac ni\right\rceil$ 所在块的右端点为 $\left\lfloor\dfrac{n-1}{\lfloor\frac{n-1}i\rfloor}\right\rfloor$。

???+ warning "注意"
    当 $i=n$ 时，上式会出现分母为 $0$ 的错误，需要特殊处理。

??? note "证明"
    $\left\lceil\dfrac ni\right\rceil=\left\lfloor\dfrac{n-1}i\right\rfloor+1$，可以发现 $n$ 的上取整分块与 $n-1$ 的下取整分块是一样的。

???+ note " 例题：[CF1954E Chain Reaction](https://codeforces.com/contest/1954/problem/E)"
    题意：有一排 $n$ 个怪兽，每个怪兽初始血量为 $a_i$，一次攻击会使一段连续的存活的怪兽血量减 $k$，血量不大于 $0$ 视作死亡，对于所有 $k$ 求出击杀所有怪兽所需攻击次数，$n,a_i\leqslant 10^5$。
    
    ??? note "思路"
        下面是一种使用二维数论分块的解法：
        
        使用 [积木大赛](https://www.luogu.com.cn/problem/P1969) 的技巧，令 $a_0=0$，对于某个 $k$，答案就是 $\sum\limits_{i=1}^n\max\left(0,\left\lceil\dfrac{a_i}{k}\right\rceil-\left\lceil\dfrac{a_{i-1}}{k}\right\rceil\right)$。
        
        对于相邻的两个怪兽，使用二维数论分块，分段求出它们对一段 $k$ 的答案的贡献，然后差分累加即可。
        
        复杂度 $O(\sum\sqrt{a_i})$。也存在其他解法。
    
    ??? note "实现"
        ```cpp
        --8<-- "docs/math/code/sqrt-decomposition/sqrt-decomposition_2.cpp"
        ```

## N 维数论分块

求含有 $\left\lfloor\dfrac {a_1}i\right\rfloor$、$\left\lfloor\dfrac {a_2}i\right\rfloor\cdots\left\lfloor\dfrac {a_n}i\right\rfloor$ 的和式时，数论分块右端点的表达式从一维的 $\left\lfloor\dfrac ni\right\rfloor$ 变为 $\min\limits_{j=1}^n\{\left\lfloor\dfrac {a_j}i\right\rfloor\}$，即对于每一个块的右端点取最小（最接近左端点）的那个作为整体的右端点。可以借助下图理解：

![多维数论分块图解](./images/n-dimension-sqrt-decomposition.png)

一般我们用的较多的是二维形式，此时可将代码中 `r = n / (n / i)` 替换成 `r = min(n / (n / i), m / (m / i))`。

## 数论分块扩展

以计算含有 $\left\lfloor\sqrt{\frac{n}{d}}\right\rfloor$ 的和式为例。考虑对于一个正整数 $n$，如何求出集合

$$
S=\left\{\left\lfloor\sqrt{\frac{n}{d}}\right\rfloor\mid d\in \mathbb{N}_{+}, d\leqslant n\right\}
$$

的所有值，以及对每一种值求出哪些 $d$ 会使其取到这个值。可以发现：

1.  因为 $\left\lfloor\sqrt{\frac{n}{d}}\right\rfloor$ 是单调不增的，所以对于所有 $v\in S$，使得 $\left\lfloor\sqrt{\frac{n}{d}}\right\rfloor=v$ 的 $d$ 必然是一段区间。
2.  对于任意正整数 $t\leqslant n$，我们对 $\leqslant t$ 与 $>t$ 的 $v\in S$ 分别分析，可以发现 $t+n/t^2\geqslant |S|$，取 $t=\sqrt[3]{n}$ 得到 $|S|$ 的一个上界为 $O(\sqrt[3]n)$。

这些结论与数论分块所需的引理相似，因此猜测可以写为数论分块形式。

结论是：使得式子

$$
\left\lfloor\sqrt{\frac{n}{p}}\right\rfloor=\left\lfloor\sqrt{\frac{n}{q}}\right\rfloor
$$

成立的最大的 $q$ 满足 $p\leqslant q\leqslant n$ 为

$$
\left\lfloor\frac{n}{\left\lfloor\sqrt{n/p}\right\rfloor^2}\right\rfloor
$$

???+ note "证明"
    令 $v=\left\lfloor\sqrt{\dfrac{n}{p}}\right\rfloor=\left\lfloor\sqrt{\dfrac{n}{q}}\right\rfloor$，那么
    
    $$
    \begin{aligned}
    v\leqslant \sqrt{\dfrac{n}{q}}&\implies v^2\leqslant \dfrac{n}{q}\\
    &\implies q\leqslant \dfrac{n}{v^2}\\
    &\implies q\leqslant \left\lfloor \dfrac{n}{v^2}\right\rfloor
    \end{aligned}
    $$
    
    同理 $p\leqslant \left\lfloor n/v^2\right\rfloor$。同时
    
    $$
    \left\lfloor \sqrt\frac{n}{\left\lfloor n/v^2\right\rfloor}\right\rfloor\geqslant \left\lfloor \sqrt\frac{n}{n/v^2}\right\rfloor=\left\lfloor v\right\rfloor=v
    $$
    
    又由 $p\leqslant \left\lfloor n/v^2\right\rfloor$ 以及单调性可推出
    
    $$
    v=\left\lfloor\sqrt{\frac{n}{p}}\right\rfloor\geqslant\left\lfloor \sqrt\frac{n}{\left\lfloor n/v^2\right\rfloor}\right\rfloor
    $$
    
    所以
    
    $$
    \left\lfloor\sqrt\frac{n}{\left\lfloor n/v^2\right\rfloor}\right\rfloor=v
    $$
    
    进而 $q=\left\lfloor n/v^2\right\rfloor$ 是最大的使得 $\left\lfloor\sqrt{n/p}\right\rfloor=\left\lfloor\sqrt{n/q}\right\rfloor$ 成立的 $q$。

故原问题可以写为数论分块形式，代码与数论分块形式并无二异。

???+ note "两个更加通用的结论"
    对于正整数 $n$ 和正实数 $\alpha, \beta$，我们有
    
    1.  对于某个不超过 $n^{\alpha/ \beta}$ 的正整数 $i$，使式子 $\left\lfloor\dfrac{n^\alpha}{i^\beta}\right\rfloor=\left\lfloor\dfrac{n^\alpha}{j^\beta}\right\rfloor$ 成立的最大的 $j$ 为 $\left\lfloor\dfrac{n^{\alpha/\beta}}{\left\lfloor n^\alpha/i^\beta \right\rfloor^{1/\beta}}\right\rfloor$。
    2.  集合 $\left\{\left\lfloor\dfrac{n^\alpha}{d^\beta}\right\rfloor: d=1,2,\dots,n\right\}$ 的大小不超过 $\min\{n,2n^{\alpha/(1+\beta)}\}$。

## 习题

1.  [CQOI2007 余数求和](https://www.luogu.com.cn/problem/P2261)（需要一点转化和特判）

2.  [UVa11526 H(n)](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=27&page=show_problem&problem=2521)（几乎可以当做模板题）

3.  [POI2007 ZAP-Queries](https://www.luogu.com.cn/problem/P3455)（数论分块一般配合 [莫比乌斯反演](./mobius.md) 用以进一步降低复杂度；本题需要用到 $[n=1]=\sum_{d|n}\mu(d)$ 这一条莫反结论）
