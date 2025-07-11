???+ warning "注意"
    下文中的欧拉数特指 Eulerian number。注意与 Euler number，以及 Euler's number（指与欧拉相关的数学常数例如 $\gamma$ 或 $\mathrm{e}$）作区分。

在计算组合中，**欧拉数**（Eulerian Number）是从 $1$ 到 $n$ 中正好满足 $m$ 个元素大于前一个元素（具有 $m$ 个「上升」的排列）条件的排列 **个数**。定义为：

$$
A(n, m) = 
\left\langle 
\begin{matrix}
  n\\
  m - 1
\end{matrix}
\right\rangle
$$

例如，从数字 $1$ 到 $3$ 一共有 $4$ 种排列使得恰好有一个元素比前一个元素大：

| 排列    | 满足条件的相邻元素   | 个数 |
| ----- | ----------- | -- |
| 1 2 3 | 1, 2 & 2, 3 | 2  |
| 1 3 2 | 1, 3        | 1  |
| 2 1 3 | 1, 3        | 1  |
| 2 3 1 | 2, 3        | 1  |
| 3 1 2 | 1, 2        | 1  |
| 3 2 1 |             | 0  |

所以按照 $A(n, m)$ 定义：如果 $n$ 等于 $3$，$m$ 等于 $1$，欧拉数值为 $4$，表示共有 $4$ 个有 $1$ 个元素大于前一个元素的排列。

对于 $n$ 和 $m$ 值比较小的欧拉数来说，我们可以直接得到结果：

| $A(n, m)$ | 满足要求的排列                                      | 个数 |
| --------- | -------------------------------------------- | -- |
| $A(1, 0)$ | $(1)$                                        | 1  |
| $A(2, 0)$ | $(2, 1)$                                     | 1  |
| $A(2, 1)$ | $(1, 2)$                                     | 1  |
| $A(3, 0)$ | $(3, 2, 1)$                                  | 1  |
| $A(3, 1)$ | $(1, 3, 2), (2, 1, 3), (2, 3, 1), (3, 1, 2)$ | 4  |
| $A(3, 2)$ | $(1, 2, 3)$                                  | 1  |

## 公式

可以通过递推或者递归的方法计算欧拉数。

首先，当 $m \geqslant n$ 或 $n = 0$ 时，没有满足条件的排列，即此时欧拉数为 $0$。

其次，当 $m = 0$ 时，只有降序的排列满足条件，即此时欧拉数为 $1$。

最后，考虑在 $n-1$ 的排列的基础上插入 $n$ 从而得到 $n$ 的排列，由于插入 $n$ 至多使欧拉数增加 $1$，所以 $A(n, m)$ 可以仅从 $A(n-1, m-1)$ 处和 $A(n-1, m)$ 处转移得到。

考虑 $n$ 插入的位置：当 $p_{i-1} < p_{i}$ 时，若将 $n$ 插到 $p_{i}$ 之前，即将 $n$ 插入到「上升」中，排列的欧拉数不变；此外，将 $n$ 插在排列之前，排列的欧拉数也不变；否则，若将 $n$ 插到其余位置，排列的欧拉数增加 $1$。

考虑从 $A(n-1, m-1)$ 转移到 $A(n, m)$，此时需要使欧拉数增加 $1$，此时不能将 $n$ 插在「上升」中或者排列开头，共有 $n - (m-1) - 1 = n-m$ 种方案。

考虑从 $A(n-1, m)$ 转移到 $A(n, m)$，此时需要欧拉数保持不变，只能将 $n$ 插在「上升」中或者排列开头，共 $m+1$ 种方案。

综上所述，有

$$
A(n, m) = \begin{cases}
    0, & m > n \text{ or } n = 0, \\
    1, & m = 0, \\
    (n-m) \cdot A(n-1, m-1) + (m+1) \cdot A(n-1, m), & \text{otherwise}.
\end{cases}
$$

## 实现

=== "C++"
    ```cpp
    int eulerianNumber(int n, int m) {
      if (m >= n || n == 0) return 0;
      if (m == 0) return 1;
      return (((n - m) * eulerianNumber(n - 1, m - 1)) +
              ((m + 1) * eulerianNumber(n - 1, m)));
    }
    ```

=== "Python"
    ```python
    def eulerianNumber(n, m):
        if m >= n or n == 0:
            return 0
        if m == 0:
            return 1
        return ((n - m) * eulerianNumber(n - 1, m - 1)) + (
            (m + 1) * eulerianNumber(n - 1, m)
        )
    ```

## 习题

-   [CF1349F1 Slime and Sequences (Easy Version)](https://codeforces.com/problemset/problem/1349/F1)
-   [CF1349F2 Slime and Sequences (Hard Version)](https://codeforces.com/problemset/problem/1349/F2)
-   [UOJ 593. 新年的军队](https://uoj.ac/problem/593)
-   [P7511 三到六](https://www.luogu.com.cn/problem/P7511)
