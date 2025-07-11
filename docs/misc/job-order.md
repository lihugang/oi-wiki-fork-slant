你有 $n$ 个任务，要求你找到一个代价最小的的顺序执行他们。第 $i$ 个任务花费的时间是 $t_i$，而第 $i$ 个任务等待 $t$ 的时间会花费 $f_i(t)$ 的代价。

形式化地说，给出 $n$ 个函数 $f_i$ 和 $n$ 个数 $t_i$，求一个排列 $p$，最小化

$$
F(p)=\sum_{i=1}^nf_{p_i}\left(\sum_{j=1}^{i-1}t_{p_j}\right)
$$

## 特殊的代价函数

### 线性代价函数

首先我们考虑所有的函数是线性的函数，即 $f_i(x)=c_ix+d_i$，其中 $c_i$ 是非负整数。显然我们可以事先把常数项加起来，因此函数就转化为了 $f_i(x)=c_ix$ 的形式。

考虑两个排列 $p$ 和 $p'$，其中 $p'$ 是把 $p$ 的第 $i$ 个位置上的数和 $i+1$ 个位置上的数交换得到的排列。则

$$
\begin{aligned}
F(p')-F(p)&=c_{p'_i}\sum_{j=1}^{i-1}t_{p'_j}+c_{p'_{i+1}}\sum_{j=1}^{i}t_{p'_j}
-\left(c_{p_i}\sum_{j=1}^{i-1}t_{p_j}+c_{p_{i+1}}\sum_{j=1}^{i}t_{p_j}\right)\\
&=c_{p_i}t_{p_{i+1}}-c_{p_{i+1}}t_{p_i}
\end{aligned}
$$

于是我们使用如果 $c_{p_i}t_{p_{i+1}}-c_{p_{i+1}}t_{p_i}>0$ 就交换的策略做一下排序就可以了。写成 $\dfrac{c_{p_i}}{t_{p_i}}>\dfrac{c_{p_{i+1}}}{t_{p_{i+1}}}$ 的形式，就可以理解为将排列按 $\dfrac{c_i}{t_i}$ 升序排序。

处理这个问题，我们的思路是考虑微扰后的变换情况，贪心地选取最优解。

### 指数代价函数

考虑代价函数的形式为 $f_i(x)=c_i\mathrm{e}^{ax}$，其中 $c_i\geqslant 0,a>0$。

我们沿用之前的思路，考虑将 $i$ 和 $i+1$ 的位置上的数交换引起的代价变化。最终得到的算法是将排列按照 $\dfrac{1-\mathrm{e}^{at_i}}{c_i}$ 升序排序。

### 相同的单增函数

我们考虑所有的 $f_i(x)$ 是同一个单增函数。那么显然我们将排列按照 $t_i$ 升序排序即可。

## Livshits–Kladov 定理

Livshits–Kladov 定理成立，当且仅当代价函数是以下三种情况：

-   线性函数：$f_i(t) = c_it + d_i$，其中 $c_i\geqslant 0$；
-   指数函数：$f_i(t) = c_i \mathrm{e}^{a t} + d_i$，其中 $c_i,a>0$；
-   相同的单增函数：$f_i(t) = \phi(t)$，其中 $\phi(t)$ 是一个单增函数。

定理是在假设代价函数足够平滑（存在三阶导数）的条件下证明的。在这三种情况下，问题的最优解可以通过简单的排序在 $O(n\log n)$ 的时间内解决。

***

**本页面主要译自博文 [Задача Джонсона с одним станком](http://e-maxx.ru/algo/johnson_problem_1) 与其英文翻译版 [Scheduling jobs on one machine](https://cp-algorithms.com/schedules/schedule_one_machine.html)。其中俄文版版权协议为 Public Domain + Leave a Link；英文版版权协议为 CC-BY-SA 4.0。**
