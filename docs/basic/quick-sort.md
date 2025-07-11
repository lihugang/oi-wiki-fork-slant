本页面将简要介绍快速排序。

## 定义

快速排序（英语：Quicksort），又称分区交换排序（英语：partition-exchange sort），简称「快排」，是一种被广泛运用的排序算法。

## 基本原理与实现

### 过程

快速排序的工作原理是通过 [分治](./divide-and-conquer.md) 的方式来将一个数组排序。

快速排序分为三个过程：

1.  将数列划分为两部分（要求保证相对大小关系）；
2.  递归到两个子序列中分别进行快速排序；
3.  不用合并，因为此时数列已经完全有序。

和归并排序不同，第一步并不是直接分成前后两个序列，而是在分的过程中要保证相对大小关系。具体来说，第一步要是要把数列分成两个部分，然后保证前一个子数列中的数都小于后一个子数列中的数。为了保证平均时间复杂度，一般是随机选择一个数 $m$ 来当做两个子数列的分界。

之后，维护一前一后两个指针 $p$ 和 $q$，依次考虑当前的数是否放在了应该放的位置（前还是后）。如果当前的数没放对，比如说如果后面的指针 $q$ 遇到了一个比 $m$ 小的数，那么可以交换 $p$ 和 $q$ 位置上的数，再把 $p$ 向后移一位。当前的数的位置全放对后，再移动指针继续处理，直到两个指针相遇。

其实，快速排序没有指定应如何具体实现第一步，不论是选择 $m$ 的过程还是划分的过程，都有不止一种实现方法。

第三步中的序列已经分别有序且第一个序列中的数都小于第二个数，所以直接拼接起来就好了。

=== "C++"
    === " 非递归实现[^ref2]"
        ```cpp
        struct Range {
          int start, end;
        
          Range(int s = 0, int e = 0) { start = s, end = e; }
        };
        
        template <typename T>
        void quick_sort(T arr[], const int len) {
          if (len <= 0) return;
          Range r[len];
          int p = 0;
          r[p++] = Range(0, len - 1);
          while (p) {
            Range range = r[--p];
            if (range.start >= range.end) continue;
            T mid = arr[range.end];
            int left = range.start, right = range.end - 1;
            while (left < right) {
              while (arr[left] < mid && left < right) left++;
              while (arr[right] >= mid && left < right) right--;
              std::swap(arr[left], arr[right]);
            }
            if (arr[left] >= arr[range.end])
              std::swap(arr[left], arr[range.end]);
            else
              left++;
            r[p++] = Range(range.start, left - 1);
            r[p++] = Range(left + 1, range.end);
          }
        }
        ```

    === "递归实现"
        ```cpp
        template <typename T>
        int Partition(T A[], int low, int high) {
          int pivot = A[low];
          while (low < high) {
            while (low < high && pivot <= A[high]) --high;
            A[low] = A[high];
            while (low < high && A[low] <= pivot) ++low;
            A[high] = A[low];
          }
          A[low] = pivot;
          return low;
        }
        template <typename T>
        void QuickSort(T A[], int low, int high) {
          if (low < high) {
            int pivot = Partition(A, low, high);
            QuickSort(A, low, pivot - 1);
            QuickSort(A, pivot + 1, high);
          }
        }
        template <typename T>
        void QuickSort(T A[], int len) {
          QuickSort(A, 0, len - 1);
        }
        ```

=== "Python[^ref2]"
    ```python
    def quick_sort(alist, first, last):
        if first >= last:
            return
        mid_value = alist[first]
        low = first
        high = last
        while low < high:
            while low < high and alist[high] >= mid_value:
                high -= 1
            alist[low] = alist[high]
            while low < high and alist[low] < mid_value:
                low += 1
            alist[high] = alist[low]
        alist[low] = mid_value
        quick_sort(alist, first, low - 1)
        quick_sort(alist, low + 1, last)
    ```

## 性质

### 稳定性

快速排序是一种不稳定的排序算法。

### 时间复杂度

快速排序的最优时间复杂度和平均时间复杂度为 $O(n\log n)$，最坏时间复杂度为 $O(n^2)$。

对于最优情况，每一次选择的分界值都是序列的中位数，此时算法时间复杂度满足的递推式为 $T(n) = 2T(\dfrac{n}{2}) + \Theta(n)$，由主定理，$T(n) = \Theta(n\log n)$。

对于最坏情况，每一次选择的分界值都是序列的最值，此时算法时间复杂度满足的递推式为 $T(n) = T(n - 1) + \Theta(n)$，累加可得 $T(n) = \Theta(n^2)$。

对于平均情况，每一次选择的分界值可以看作是等概率随机的。

??? note "证明"
    下面我们来证明这种情况下算法的时间复杂度是 $O(n\log n)$。
    
    **引理 1：** 当对 $n$ 个元素的数组进行快速排序时，假设在划分元素时总共的比较次数为 $X$，则快速排序的时间复杂度是 $O(n + X)$。
    
    由于在每次划分元素的过程中，都会选择一个元素作为分界，所以划分元素的过程至多发生 $n$ 次。又由于划分元素的过程中比较的次数和其他基础操作的次数在一个数量级，所以总时间复杂度是 $O(n + X)$ 的。
    
    设 $a_i$ 为原数组中第 $i$ 小的数，定义 $A_{i,j}$ 为 $\{ a_i, a_{i+1}, \dots, a_j \}$，$X_{i,j}$ 是一个取值为 $0$ 或者 $1$ 的离散随机变量表示在排序过程中 $a_i$ 是否和 $a_j$ 发生比较。
    
    显然每次选取的分界值是不同的，而元素只会和分界值比较，所以总比较次数
    
    $$
    \begin{aligned} X = \sum \limits _ {i = 1} ^ {n - 1} \sum \limits _ {j = i + 1} ^ n X_{i,j} \end{aligned}
    $$
    
    由期望的线性性，
    
    $$
    \begin{aligned} E[X] & = E \left[ \sum \limits _ {i = 1} ^ {n - 1} \sum \limits _ {j = i + 1} ^ n X_{i,j} \right] \\ & = \sum \limits _ {i = 1} ^ {n - 1} \sum \limits _ {j = i + 1} ^ n E[X_{i,j}] \\ & = \sum \limits _ {i = 1} ^ {n - 1} \sum \limits _ {j = i + 1} ^ n P(a_i\ \text{和}\ a_j\ \text{比较}) \end{aligned}
    $$
    
    **引理 2：** $a_i$ 和 $a_j$ 比较的充要条件是 $a_i$ 或 $a_j$ 是集合 $A_{i,j}$ 中第一个被选中的分界值。
    
    先证必要性，即若 $a_i$ 和 $a_j$ 都不是集合 $A_{i,j}$ 中第一个被选中的分界值，则 $a_i$ 不和 $a_j$ 比较。
    
    若 $a_i$ 和 $a_j$ 都不是集合 $A_{i,j}$ 中第一个被选中的分界值，则一定存在一个 $x$ 满足 $i < x < j$，使得 $a_x$ 是 $A_{i,j}$ 中第一个被选中的分界值。在以 $a_x$ 为分界值的划分中，$a_i$ 和 $a_j$ 被划分到数组的两个不同的子序列中，所以之后 $a_i$ 和 $a_j$ 一定不会比较。又因为元素只和分界值比较，所以 $a_i$ 和 $a_j$ 在此次划分前和划分中没有比较。所以 $a_i$ 不和 $a_j$ 比较。
    
    再证充分性，即若 $a_i$ 或 $a_j$ 是集合 $A_{i,j}$ 中第一个被选中的分界值，则 $a_i$ 和 $a_j$ 比较。
    
    不失一般地，假设 $a_i$ 是集合 $A_{i,j}$ 中第一个被选中的分界值。由于 $A_{i,j}$ 中没有其他数选为分界值，所以 $A_{i,j}$ 中的元素都在数组的同一子序列中。在以 $a_i$ 为分界值的划分中，$a_i$ 和当前子序列中所有元素都进行了比较，所以 $a_i$ 和 $a_j$ 进行了比较。
    
    考虑计算 $P(a_i\ \text{和}\ a_j\ \text{比较})$。在 $A_{i,j}$ 中某个元素被选为分界值之前，$A_{i,j}$ 中的元素都在数组的同一子序列中。所以 $A_{i,j}$ 中每个元素都会被等可能地第一个被选为分界值。由于 $A_{i,j}$ 中有 $j - i + 1$ 个元素，由引理 2，
    
    $$
    P(a_i \text{和} a_j \text{比较}) = P(a_i \text{或} a_j \text{是集合} A_{i,j} \text{中第一个被选中的分界值}) = \dfrac{2}{j-i+1}
    $$
    
    所以
    
    $$
    \begin{aligned} E[X] & = \sum \limits _ {i = 1} ^ {n - 1} \sum \limits _ {j = i + 1} ^ n P(a_i\ \text{和}\ a_j\ \text{比较}) \\ & = \sum \limits _ {i = 1} ^ {n - 1} \sum \limits _ {j = i + 1} ^ n \dfrac{2}{j - i + 1} \\ & = \sum \limits _ {i = 1} ^ {n - 1} \sum \limits _ {k = 2} ^ {n - i + 1} \dfrac{2}{k} \\ & = \sum \limits _ {i = 1} ^ {n - 1} O(\log n) \\ & = O(n \log n) \end{aligned}
    $$
    
    由此，快速排序的期望时间复杂度为 $O(n \log n)$。

在实践中，几乎不可能达到最坏情况，而快速排序的内存访问遵循局部性原理，所以多数情况下快速排序的表现大幅优于堆排序等其他复杂度为 $O(n \log n)$ 的排序算法。[^ref1]

## 优化

### 朴素优化思想

如果仅按照上文所述的基本思想来实现快速排序（或者是直接照抄模板）的话，那大概率是通不过 [P1177【模板】快速排序](https://www.luogu.com.cn/problem/P1177) 这道模板的。因为有毒瘤数据能够把朴素的快速排序卡成 $O(n^2)$。

所以，我们需要对朴素快速排序思想加以优化。较为常见的优化思路有以下三种[^ref3]。

-   通过 **三数取中（即选取第一个、最后一个以及中间的元素中的中位数）** 的方法来选择两个子序列的分界元素（即比较基准）。这样可以避免极端数据（如升序序列或降序序列）带来的退化；
-   当序列较短时，使用 **插入排序** 的效率更高；
-   每趟排序后，**将与分界元素相等的元素聚集在分界元素周围**，这样可以避免极端数据（如序列中大部分元素都相等）带来的退化。

下面列举了几种较为成熟的快速排序优化方式。

### 三路快速排序

#### 定义

三路快速排序（英语：3-way Radix Quicksort）是快速排序和 [基数排序](./radix-sort.md) 的混合。它的算法思想基于 [荷兰国旗问题](https://en.wikipedia.org/wiki/Dutch_national_flag_problem) 的解法。

#### 过程

与原始的快速排序不同，三路快速排序在随机选取分界点 $m$ 后，将待排数列划分为三个部分：小于 $m$、等于 $m$ 以及大于 $m$。这样做即实现了将与分界元素相等的元素聚集在分界元素周围这一效果。

#### 性质

三路快速排序在处理含有多个重复值的数组时，效率远高于原始快速排序。其最佳时间复杂度为 $O(n)$。

#### 实现

三路快速排序实现起来非常简单，下面给出了一种三路快排的 C++ 实现。

=== "C++"
    ```cpp
    // 模板的 T 参数表示元素的类型，此类型需要定义小于（<）运算
    template <typename T>
    // arr 为需要被排序的数组，len 为数组长度
    void quick_sort(T arr[], const int len) {
      if (len <= 1) return;
      // 随机选择基准（pivot）
      const T pivot = arr[rand() % len];
      // i：当前操作的元素下标
      // arr[0, j)：存储小于 pivot 的元素
      // arr[k, len)：存储大于 pivot 的元素
      int i = 0, j = 0, k = len;
      // 完成一趟三路快排，将序列分为：
      // 小于 pivot 的元素 | 等于 pivot 的元素 | 大于 pivot 的元素
      while (i < k) {
        if (arr[i] < pivot)
          swap(arr[i++], arr[j++]);
        else if (pivot < arr[i])
          swap(arr[i], arr[--k]);
        else
          i++;
      }
      // 递归完成对于两个子序列的快速排序
      quick_sort(arr, j);
      quick_sort(arr + k, len - k);
    }
    ```

=== "Python[^ref2]"
    ```python
    def quick_sort(arr, l, r):
        if l >= r:
            return
        random_index = random.randint(l, r)
        pivot = arr[random_index]
        arr[l], arr[random_index] = arr[random_index], arr[l]
        i = l + 1
        j = l
        k = r + 1
        while i < k:
            if arr[i] < pivot:
                arr[i], arr[j + 1] = arr[j + 1], arr[i]
                j += 1
                i += 1
            elif arr[i] > pivot:
                arr[i], arr[k - 1] = arr[k - 1], arr[i]
                k -= 1
            else:
                i += 1
        arr[l], arr[j] = arr[j], arr[l]
        quick_sort(arr, l, j - 1)
        quick_sort(arr, k, r)
    ```

### 内省排序

#### 定义

内省排序（英语：Introsort 或 Introspective sort）[^ref4]是快速排序和 [堆排序](./heap-sort.md) 的结合，由 David Musser 于 1997 年发明。内省排序其实是对快速排序的一种优化，保证了最差时间复杂度为 $O(n\log n)$。

#### 性质

内省排序将快速排序的最大递归深度限制为 $\lfloor \log_2n \rfloor$，超过限制时就转换为堆排序。这样既保留了快速排序内存访问的局部性，又可以防止快速排序在某些情况下性能退化为 $O(n^2)$。

#### 实现

从 2000 年 6 月起，SGI C++ STL 的 `stl_algo.h` 中 `sort()` 函数的实现采用了内省排序算法。

## 线性找第 k 大的数

在下面的代码示例中，第 $k$ 大的数被定义为序列排成升序时，第 $k$ 个位置上的数（编号从 0 开始）。

找第 $k$ 大的数（K-th order statistic），最简单的方法是先排序，然后直接找到第 $k$ 大的位置的元素。这样做的时间复杂度是 $O(n\log n)$，对于这个问题来说很不划算。

我们可以借助快速排序的思想解决这个问题。考虑快速排序的划分过程，在快速排序的「划分」结束后，数列 $A_{p} \cdots A_{r}$ 被分成了 $A_{p} \cdots A_{q}$ 和 $A_{q+1} \cdots A_{r}$，此时可以按照左边元素的个数（$q - p + 1$）和 $k$ 的大小关系来判断是只在左边还是只在右边递归地求解。

和快速排序一样，该方法的时间复杂度依赖于每次划分时选择的分界值。如果采用随机选取分界值的方式，可以证明在期望意义下，程序的时间复杂度为 $O(n)$。

### 实现（C++）

```cpp
// 模板的 T 参数表示元素的类型，此类型需要定义小于（<）运算
template <typename T>
// arr 为查找范围数组，rk 为需要查找的排名（从 0 开始），len 为数组长度
T find_kth_element(T arr[], int rk, const int len) {
  if (len <= 1) return arr[0];
  // 随机选择基准（pivot）
  const T pivot = arr[rand() % len];
  // i：当前操作的元素下标
  // arr[0, j)：存储小于 pivot 的元素
  // arr[k, len)：存储大于 pivot 的元素
  int i = 0, j = 0, k = len;
  // 完成一趟三路快排，将序列分为：
  // 小于 pivot 的元素 ｜ 等于 pivot 的元素 ｜ 大于 pivot 的元素
  while (i < k) {
    if (arr[i] < pivot)
      swap(arr[i++], arr[j++]);
    else if (pivot < arr[i])
      swap(arr[i], arr[--k]);
    else
      i++;
  }
  // 根据要找的排名与两条分界线的位置，去不同的区间递归查找第 k 大的数
  // 如果小于 pivot 的元素个数比k多，则第 k 大的元素一定是一个小于 pivot 的元素
  if (rk < j) return find_kth_element(arr, rk, j);
  // 否则，如果小于 pivot 和等于 pivot 的元素加起来也没有 k 多，
  // 则第 k 大的元素一定是一个大于 pivot 的元素
  else if (rk >= k)
    return find_kth_element(arr + k, rk - k, len - k);
  // 否则，pivot 就是第 k 大的元素
  return pivot;
}
```

### 改进：中位数中的中位数

中位数中的中位数（英文：Median of medians），提供了一种确定性的选择划分过程中分界值的方法，从而能够让找第 $k$ 大的数算法在最坏情况下也能实现线性时间复杂度。

该算法的流程如下：

1.  将整个序列划分为 $\left \lfloor \dfrac{n}{5} \right \rfloor$ 组，每组元素数不超过 5 个；
2.  寻找每组元素的中位数（因为元素个数较少，可以直接使用 [插入排序](./insertion-sort.md) 等算法）。
3.  找出这 $\left \lfloor \dfrac{n}{5} \right \rfloor$ 组元素中位数中的中位数。将该元素作为前述算法中每次划分时的分界值即可。

#### 时间复杂度证明

下面将证明，该算法在最坏情况下的时间复杂度为 $O(n)$。设 $T(n)$ 为问题规模为 $n$ 时，解决问题需要的计算量。

先分析前两步——划分与寻找中位数。由于划分后每组内的元素数量非常少，可以认为寻找一组元素的中位数的时间复杂度为 $O(1)$。因此找出所有 $\left \lfloor \dfrac{n}{5} \right \rfloor$ 组元素中位数的时间复杂度为 $O(n)$。

接下来分析第三步——递归过程。这一步进行了两次递归调用：第一次是寻找各组中位数中的中位数，需要的开销显然为 $T(\dfrac{n}{5})$，第二次是进入分界值的左侧部分或右侧部分。根据我们选取的划分元素，有 $\dfrac{1}{2} \times \left \lfloor \dfrac{n}{5} \right \rfloor = \left \lfloor \dfrac{n}{10} \right \rfloor$ 组元素的中位数小于分界值，这几组元素中，比中位数还小的元素也一定比分界值要小，从而整个序列中小于分界值的元素至少有 $3 \times \left \lfloor \dfrac{n}{10} \right \rfloor = \left \lfloor \dfrac{3n}{10} \right \rfloor$ 个。同理，整个序列中大于分界值的元素也至少有 $\left \lfloor \dfrac{3n}{10} \right \rfloor$ 个。因此，分界值的左边或右边至多有 $\dfrac{7n}{10}$ 个元素，这次递归的时间开销的上界为 $T(\dfrac{7n}{10})$。

综上，我们可以列出这样的不等式：

$$
T(n) \leqslant T(\dfrac{n}{5}) + T(\dfrac{7n}{10}) + O(n)
$$

假设 $T(n) = O(n)$ 在问题规模足够小时成立。根据定义，此时有 $T(n) \leqslant cn$，其中 $c$ 为一正常数。将不等式右边的所有 $T(n)$ 进行代换：

$$
\begin{aligned}
T(n) & \leqslant T(\dfrac{n}{5}) + T(\dfrac{7n}{10}) + O(n)\\
     & \leqslant \dfrac{cn}{5} + \dfrac{7cn}{10} + O(n)\\
     & \leqslant \dfrac{9cn}{10} + O(n)\\
     & = O(n)
\end{aligned}
$$

到这里我们就证明了，该算法在最坏情况下也具有 $O(n)$ 的时间复杂度。

## 参考资料与注释

[^ref1]: [C++ 性能榨汁机之局部性原理 - I'm Root lee !](http://irootlee.com/juicer_locality/)

[^ref2]: [算法实现/排序/快速排序 - 维基教科书，自由的教学读本](https://zh.wikibooks.org/wiki/%E7%AE%97%E6%B3%95%E5%AE%9E%E7%8E%B0/%E6%8E%92%E5%BA%8F/%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F)

[^ref3]: [三种快速排序以及快速排序的优化](https://blog.csdn.net/insistGoGo/article/details/7785038)

[^ref4]: [introsort](https://en.wikipedia.org/wiki/Introsort)
