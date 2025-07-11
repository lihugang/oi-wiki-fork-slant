## 定义

状压 DP 是动态规划的一种，通过将状态压缩为整数来达到优化转移的目的。

## 例题 1

???+ note "[「SCOI2005」互不侵犯](https://loj.ac/problem/2153)"
    在 $N\times N$ 的棋盘里面放 $K$ 个国王（$1 \leqslant N \leqslant 9, 1 \leqslant K \leqslant N \times N$），使他们互不攻击，共有多少种摆放方案。
    
    国王能攻击到它上下左右，以及左上左下右上右下八个方向上附近的各一个格子，共 $8$ 个格子。

### 解释

设 $f(i,j,l)$ 表示前 $i$ 行，第 $i$ 行的状态为 $j$，且棋盘上已经放置 $l$ 个国王时的合法方案数。

对于编号为 $j$ 的状态，我们用二进制整数 $sit(j)$ 表示国王的放置情况，$sit(j)$ 的某个二进制位为 $0$ 表示对应位置不放国王，为 $1$ 表示在对应位置上放置国王；用 $sta(j)$ 表示该状态的国王个数，即二进制数 $sit(j)$ 中 $1$ 的个数。例如，如下图所示的状态可用二进制数 $100101$ 来表示（棋盘左边对应二进制低位），则有 $sit(j)=100101_{(2)}=37, sta(j)=3$。

![](./images/SCOI2005-互不侵犯.png)

设当前行的状态为 $j$，上一行的状态为 $x$，可以得到下面的状态转移方程：$f(i,j,l) = \sum f(i-1,x,l-sta(j))$。

设上一行的状态编号为 $x$，在保证当前行和上一行不冲突的前提下，枚举所有可能的 $x$ 进行转移，转移方程：

$$
f(i,j,l) = \sum f(i-1,x,l-sta(j))
$$

### 实现

??? "参考代码"
    ```cpp
    --8<-- "docs/dp/code/state/state_1.cpp"
    ```

## 例题 2

???+ note "[\[POI2004\] PRZ](https://www.luogu.com.cn/problem/P5911)"
    有 $n$ 个人需要过桥，第 $i$ 的人的重量为 $w_i$，过桥用时为 $t_i$. 这些人过桥时会分成若干组，只有在某一组的所有人全部过桥后，其余的组才能过桥。桥最大承重为 $W$，问这些人全部过桥的最短时间。
    
    $100\leqslant W \leqslant 400$，$1\leqslant n\leqslant 16$，$1\leqslant t_i\leqslant 50$，$10\leqslant w_i\leqslant 100$.

### 解释

我们用 $S$ 表示所有人构成集合的一个子集，设 $t(S)$ 表示 $S$ 中人的最长过桥时间，$w(S)$ 表示 $S$ 中所有人的总重量，$f(S)$ 表示 $S$ 中所有人全部过桥的最短时间，则：

$$
\begin{cases}
    f(\varnothing)=0,\\
    f(S)=\min\limits_{T\subseteq S;~w(T)\leqslant W}\left\{t(T)+f(S\setminus T)\right\}.
\end{cases}
$$

需要注意的是这里不能直接枚举集合再判断是否为子集，而应使用 [子集枚举](../math/binary-set.md#遍历所有掩码的子掩码)，从而使时间复杂度为 $O(3^n)$.

### 实现

??? "参考代码"
    ```cpp
    --8<-- "docs/dp/code/state/state_2.cpp"
    ```

## 习题

-   [「NOI2001」炮兵阵地](https://loj.ac/problem/10173)
-   [「USACO06NOV」玉米田 Corn Fields](https://www.luogu.com.cn/problem/P1879)
-   [「九省联考 2018」一双木棋](https://loj.ac/problem/2471)
