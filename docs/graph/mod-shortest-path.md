当出现形如「给定 $n$ 个整数，求这 $n$ 个整数能拼凑出多少的其他整数（$n$ 个整数可以重复取）」，以及「给定 $n$ 个整数，求这 $n$ 个整数不能拼凑出的最小（最大）的整数」，或者「至少要拼几次才能拼出模 $K$ 余 $p$ 的数」的问题时可以使用同余最短路的方法。

同余最短路利用同余来构造一些状态，可以达到优化空间复杂度的目的。

类比 [差分约束](./diff-constraints.md) 方法，利用同余构造的这些状态可以看作单源最短路中的点。同余最短路的状态转移通常是这样的 $f(i+y) = f(i) + y$，类似单源最短路中 $f(v) = f(u) +edge(u,v)$。

## 例题

### 例题一

???+ note "[P3403 跳楼机](https://www.luogu.com.cn/problem/P3403)"
    题目大意：给定 $x，y，z，h$，对于 $k \in [1,h]$，有多少个 $k$ 能够满足 $ax+by+cz=k$。（$0\leqslant a,b,c$，$1\leqslant x,y,z\leqslant 10^5$，$h\leqslant 2^{63}-1$）

不妨假设 $x < y < z$。

令 $d_i$ 为只通过 **操作 2** 和 **操作 3**，需满足 $p\bmod x = i$ 能够达到的最低楼层 $p$，即 **操作 2** 和 **操作 3** 操作后能得到的模 $x$ 下与 $i$ 同余的最小数，用来计算该同余类满足条件的数个数。

可以得到两个状态：

-   $i \xrightarrow{y} (i+y) \bmod x$

-   $i \xrightarrow{z} (i+z) \bmod x$

注意通常选取一组 $a_i$ 中最小的那个数对它取模，也就是此处的 $x$，这样可以尽量减小空间复杂度（剩余系最小）。

那么实际上相当于执行了最短路中的建边操作：

`add(i, (i+y) % x, y)`

`add(i, (i+z) % x, z)`

接下来只需要求出 $d_0, d_1, d_2, \dots, d_{x-1}$，只需要跑一次最短路就可求出相应的 $d_i$。

与差分约束问题相同，当存在一组解 $\{a_1,a_2,\cdots,a_n\}$ 时，$\{a_1+d,a_2+d,\cdots,a_n+d\}$ 同样为一组解，因此在该题让 $i=1$ 作为源点，此时源点处的 $dis_{1}=1$ 在已知范围内最小，因此得到的也是一组最小的解。

答案即为：

$$
\sum_{i=0}^{x-1}\left(\frac{h-d_i}{x} + 1\right)
$$

加 1 是由于 $d_i$ 所在楼层也算一次。

代码实现上注意到 $h$ 的范围是 $h \leqslant 2^{63}-1$，所以在求解最短路之前 $d_i$ 的初始值应至少设为 $2^{63}$，这超过了 C++ 中 `long long` 的最大值。所以可以使用 `unsigned long long` 或者先把 $h \gets h - 1$，然后把最低楼层设为 $0$ 层，其他代码无异。

???+ note "参考实现"
    ```cpp
    --8<-- "docs/graph/code/mod-shortest-path/mod-shortest-path_1.cpp"
    ```

### 例题二

???+ note "[ARC084B Small Multiple](https://atcoder.jp/contests/arc084/tasks/arc084_b)"
    题目大意：给定 $n$，求 $n$ 的倍数中，数位和最小的那一个的数位和。（$1\leqslant n\leqslant 10^5$）

本题可以使用循环卷积优化完全背包在 $O(n\log^2 n)$ 的时间内解决，但我们希望得到线性的算法。

观察到任意一个正整数都可以从 $1$ 开始，按照某种顺序执行乘 $10$、加 $1$ 的操作，最终得到，而其中加 $1$ 操作的次数就是这个数的数位和。这提示我们使用最短路。

对于所有 $0\leqslant k\leqslant n-1$，从 $k$ 向 $10k$ 连边权为 $0$ 的边；从 $k$ 向 $k+1$ 连边权为 $1$ 的边。（点的编号均在模 $n$ 意义下）

每个 $n$ 的倍数在这个图中都对应了 $1$ 号点到 $0$ 号点的一条路径，求出 $1$ 到 $0$ 的最短路即可。某些路径不合法（如连续走了 $10$ 条边权为 $1$ 的边），但这些路径产生的答案一定不优，不影响答案。

时间复杂度 $O(n)$。

## 习题

[洛谷 P3403 跳楼机](https://www.luogu.com.cn/problem/P3403)

[洛谷 P2662 牛场围栏](https://www.luogu.com.cn/problem/P2662)

[\[国家集训队\] 墨墨的等式](https://www.luogu.com.cn/problem/P2371)

[「NOIP2018」货币系统](https://loj.ac/problem/2951)

[AGC057D - Sum Avoidance](https://atcoder.jp/contests/agc057/tasks/agc057_d)

[「THUPC 2023 初赛」背包](https://loj.ac/p/6872)
