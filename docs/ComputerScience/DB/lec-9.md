---
    comments: true
    statistics: true
---

# Query Processing

!!! info Overview 

    在数据库系统中进行查询会有以下几个基本步骤：

    1. **Parsing and translation**: 将查询语句转换为关系代数表达式
    2. **Optimization**: 一个查询语句可能对应多个等价的关系代数表达式，并且对于每一个操作算子（如连接、投影等）可能会有不同的实现方式，我们希望选择最优的执行计划
    3. **Evaluation**：执行查询计划，返回结果

    <figure markdown="span">
        ![](./assets/查询处理1.png){width=70%}
    </figure>

## Measures of Query Cost

Cost is generally measured as total elapsed time for answering query.

影响一次查询耗时的因素可能有很多，我们一般主要关注 Disk Access、CPU Time 和 Network Time。其中 Disk Access 是主导因素，我们主要考虑以下三个角度：

- number of seek operations
- number of blocks read × average block read cost
- number of blocks written × average block write cost
    - 一般而言 write cost 会比 read cost 大很多，因为我们在写入内存之后还需要再把数据读出来，以保证数据的正确性和一致性。

为了简便起见，我们只考虑从磁盘中传输的数据块的数量寻址（seek）的次数

- $t_T$：time to transfer one block
- $t_S$：time for one seek

例如我们要传输 b 个块，并需要 s 次寻址，那么总的时间为：$T = b \cdot t_T + s \cdot t_S$

!!! tip
    - 为了简洁期间，我们没有考虑 CPU 的时间开销，但在实际的系统中，我们也需要把这一部分考虑进去
    - 在后面的公式中，把最终结果写入磁盘的开销都没有被考虑进去

!!! note "内存缓冲区对查询成本的影响"
    查询的成本很大程度依赖于主存的缓冲区大小：

    - 内存越大，访问磁盘所需的次数就越少
    - 操作系统中会有其他进程并行执行，因此在实际执行前很难确定缓冲区中实际可用的内存大小
    - 通常我们会同时考虑最坏情况的缓冲区大小（假设我们只有操作所需的最小内存空间可用）和最佳情况的缓冲区大小
    - 访问所需的数据可能已经驻留在缓冲区中，从而避免了磁盘 I/O 操作，但在估算成本时我们很难将其考虑在内

## Selection Operation 

### Basic algorithms

**File scan**: 简单粗暴地逐行扫描文件，检查每一行是否满足条件，*不使用索引*。下面介绍的 A1 和 A2 算法都是基于文件扫描的。

- **A1 - Linear Search**:扫描每一个文件块并测试所有记录，检查他们是否满足选择条件。
    - Cost estimate = $b_r$ block transfers + 1 seek
        - $b_r$ 是包含关系 $r$ 中记录的块数
    - 如果选择条件是基于键属性的，那么当我们找到这个记录之后就可以停止扫描
        - 平均开销是扫描一半的块：cost estimate = $(b_r/2)$ block transfers + 1 seek
    - 线性搜索可以用于任何选择条件、记录在文件的任何排序方式、无需考虑是否存在索引

- **A2 - Binary Search**: 适用于有序文件，并且要求选择条件是一个等值比较（equality comparison）。
    - 我们需要假设保存这个关系的文件是连续的
    - Cost estimate = $\lceil \log_2 (b_r) \rceil$ block transfers + $\lceil \log_2 (b_r) \rceil$ seeks
        - 每一次传输一个块都需要进行一次寻址
        - time cost = $\lceil \log_2 (b_r) \rceil \times (t_T + t_S)$
    - 如果选择不是基于键属性的，那么我们还需要加上包含满足选择条件的记录的块数 $b_s$，因此总的块传输数量为

        block transfer = $\lceil \log_2 (b_r) \rceil + (\lceil sc(A, r) / f_r \rceil - 1)$

        其中 $sc(A, r)$ 是满足选择条件的记录数，$f_r$ 是每个块中记录的数量。

        即完成的传输成本 = 定位第一个满足条件的块并传输 + 传输剩余所有满足条件的块（此时第一个块已经被传输好了，因此要减一）

!!! note
    二分搜索通常不适用于数据库中的查找

    - 数据通常不是连续存储的
    - 需要存在可用的索引
    - 二分搜索比索引搜索需要更多的寻址操作

### Selections Using Indices and equality

**Index scan**: 使用索引的搜索算法，并且要求选择条件必须基于索引的 search key。

- **A3 - primary index, equality on key**
    - 要求在关系的键属性上建立了主索引，并且查询是根据键值的等值条件获取单个记录
    - cost = $(h_i + 1) \times (t_T + t_S)$
        - $h_i$ 是索引树的高度，即有多少层索引
        - 我们需要对索引项进行 $h_i$ 次读取操作，最后还需要一次额外的读取操作了访问包含目标记录的数据块

    <figure markdown="span">
        ![](./assets/查询处理5.png){width=70%}
    </figure>

- **A4 - primary index, equality on nonkey**
    - 适用于主索引，并且查询条件是基于非键属性的等值条件获取多个记录
    - 需要记录被保存在连续的块中，假设有 $b$ 个块包含有满足条件的记录
    - cost = $h_i \times (t_T + t_S) + t_S + b \times t_T$
        - $h_i$ 是索引树的高度，即有多少层索引
        - $b$ 是满足条件的记录所在的块数
        - $h_i \times (t_T + t_S)$ 表示通过索引找到第一个匹配的索引项，$t_S$ 表示把磁头定位到第一个匹配的块，$b \times t_T$ 表示传输所有满足条件的块

    <figure markdown="span">
        ![](./assets/查询处理2.png){width=70%}
    </figure>

- **A5 - secondary index, equality on key**
    - 如果搜索键是候选键（检索单条记录），那么

        cost = $(h_i + 1) \times (t_T + t_S)$

    <figure markdown="span">
        ![](./assets/查询处理3.png){width=70%}
    </figure>

    - 如果搜索键不是候选键（检索多条记录），那么匹配条件的记录可能位于不同的数据块中

        cost = $(h_i + m + n) \times (t_T + t_S)$

        - 我们使用索引得到的是若干个放有指向数据块的指针的块，$m$ 放有指针的块的数量，$n$ 是磁盘中匹配的记录的总数目
        - 这种情况的开销可能非常大，甚至比线性扫描更糟糕

    <figure markdown="span">
        ![](./assets/查询处理4.png){width=70%}
    </figure>

### Selections Involving Comparisons

- **A6 - primary index, comparison**
    - 对于 $\sigma_{A \geqslant v}(r)$ 的情况，先使用索引找到第一个满足 $A \geqslant v$ 的记录，然后那里开始顺序扫描记录
        - cost = $h_i \times (t_T + t_S) + t_S + b \times t_T$
    - 对于 $\sigma_{A \leqslant v}(r)$ 的情况，直接从头开始扫描关系，直到遇见第一个满足 $A > v$ 的记录为止，不使用索引
        - cost = $t_S + b \times t_T$

- **A7 - secondary index, comparison**
    - 对于 $\sigma_{A \geqslant v}(r)$ 的情况，先使用索引找到第一个满足 $A \geqslant v$ 的索引项，然后那里开始顺序扫描索引项，使用指针依次访问记录
    - 对于 $\sigma_{A \leqslant v}(r)$ 的情况，直接从索引树的第一个叶子节点开始扫描，依次访问各个记录，直到遇见第一个满足 $A > v$ 的索引项位置
    - 在上述两种情况下，由于我们需要使用指针，因此访问每个记录都需要一次单独的 I/O 操作，使用线性扫描可能开销会较小

### Implementation of Complex Selections

这种情况下，选择操作需要考虑多个条件，即结果是多个条件的合取 $\sigma_{\theta_1 \wedge \cdots \wedge \theta_n}(r)$

- **A8 - conjunctive selection using one index**
    - 选择一个 $\theta_i$，并且从算法 A1-7 中选择一个，先执行代价最小的一个 $\sigma(\theta_i)$
    - 把第一步得到的结果从内存缓冲区中取出，应用其他选择条件 $\theta$，以此类推

- **A9 - conjunctive selection using composite index**
    - 如果存在可用的组合索引（多键索引）的话，就直接使用它来同时处理多个条件
    - 使用多键索引比对每个条件分别单独使用索引更高效

- **A10 - conjunctive selection by intersection of identifiers**
    - 需要索引带有指向记录的指针
    - 单独对每个索引进行查询，然后查看结果的交集
- **A10 - disjunctive selection by union of identifiers**
    - $\sigma_{\theta_1 \vee \cdots \vee \theta_n}(r)$
    - 每个选择条件都有可用的索引时才可以应用，否则只能直接使用先行扫描
    - 对每个条件分别使用对应的索引，然后对得到的指向记录的指针取并集
    - 最后从文件中读取这些数据

- 否定条件的选择形式为 $\sigma_{\neg \theta}(r)$
    - 通常对文件使用线性扫描
    - 如果只有很少的记录满足条件 $\neg \theta$，并且 $\theta$ 有可用的索引
        - 那么就先用索引找到满足 $\theta$ 的记录，然后通过计算补集得到满足 $\neg \theta$ 的记录

## Sorting 

!!! info
    - 当一个关系可以放在内存中时，我们可以对它使用快速排序等排序方法
    - 但当关系的太大以至于无法完整放入内存时，我们就需要使用外部归并排序（external merge sort）

    > 关于外部排序的具体细节可以参考我的[ADS笔记](../ADS/lec-15.md)中关于外部排序的部分，在下面的内容就不会对算法的具体细节做太多展开。

假设内存大小为 $M$ 个 page，外部归并排序的操作如下：

1. 创建若干个有序的 runs（可译为顺串，或段）
    - 初始化 $i$ 为 0
    - 重复以下步骤直到处理完整个关系
        - 读取 $M$ 个数据块到内存中
        - 在内存中对这些数据块排序
        - 将排序后的数据写入顺串 $R_i$ 中（这里需要把数据写回磁盘），令 $i$ 递增 1
    - 记最后 $i$ 的大小为 $N$
2. 合并顺串（N-路合并）
    
    这里我们假设 $N < M$，即顺串的数量小于内存可以容纳的数据块的个数

    - 使用内存中的 $N$ 个块作为输入缓冲区，一个块作为输出缓冲区。从每个顺串（run）中读取第一个数据块到各自的缓冲区中。
    - 重复执行以下步骤：
        1. 根据排序的次序从所有的缓冲页中选择第一块记录（如按升序或降序排列）
        2. 把这个记录块写入输出缓冲区中（输出缓冲区满时写入磁盘中）
        3. 把这个记录从输入缓冲区页中删除
            - 如果此时这个缓冲区空了，那么就从它对应的顺串中读取下一块数据
    - 直到所有的输入缓冲区都为空时此程序停止

如果 $N \geqslant M$，那么就需要多次合并传递（pass）的过程：

- 在每一次传递过程中，所有连续的 $M-1$ 个顺串都会被合并为 $1$ 个
- 每次传递都会使得顺串的数量变为原来的 $\dfrac{1}{M-1}$，并且使得顺串的长度变为原来的 $M-1$ 倍
    - 例如 $M=11$ 时，如果有 90 个顺串，那么经过一次 pass 后就只有 9 个顺串了，并且每个顺串都是初始顺串的 10 倍长（假设原先的所有顺串都有相同的长度）
- 我们需要重复上述步骤直到只剩下一个顺串

!!! example 
    <figure markdown="span">
        ![](./assets/查询处理6.png){width=50%}
    </figure>

!!! note
    1. 总共需要 $\left\lceil \log_{M-1}(\dfrac{b_r}{M}) \right\rceil$ 次合并传递的过程，其中 $b_r$ 是数据块的个数
    2. **块传递次数**：创建初始顺串和每次合并传递都需要 $2b_r$ 次块传递操作：把数据从磁盘中读入内存（一次读取），再把结果写回磁盘形成有序顺串（一次写入）
        - 例外是最后一次合并传递操作，因为最终的结果可能会直接传递给下一个操作，而不需要写回磁盘
        - 因此外部所需的数据块传递次数为 
            $$ b_r \times \left(2 \left\lceil \log_{M-1}(\dfrac{b_r}{M}) \right\rceil + 1 \right) $$
    3. **寻道次数**：
        - 在创建顺串时，每个顺串都需要一次寻道来读取数据，以及一次寻道来写回数据
            - 由于共有 $\lceil b_r / M\rceil$ 个顺串，因此需要 $2\lceil b_r / M \rceil$ 次寻道
        - 在合并时，我们假设缓冲区大小为 $b_b$（即一次能读/写 $b_b$ 个块）
            - 那么每次合并传递都需要 $2\lceil b_r/b_b \rceil$ 次寻道（最后一次合并传递不需要写回，因此可以少计算一次寻道）
        - 因此总共的寻道次数是
            $$ 2\lceil b_r / M\rceil + \lceil b_r/b_b \rceil (2 \lceil \log_{M-1}(b_r / M) \rceil - 1) $$

## Join Operation 

连接操作几乎可以认为是关系数据库中最重要的操作，它可以把两个关系中满足特定条件的元组组合起来得到新的关系。

实现连接操作的算法有以下几种：

- Nested-loop join
- Block nested-loop join
- Indexed nested-loop join
- Merge-join
- Hash-join

### Nested-loop join

嵌套循环连接是最简单的连接算法：

```python
for each tuple t_r in r do begin
    for each tuple t_s in s do begin
        test pair (t_r, t_s) to see if they satisfy the join condition θ
        if they do, add t_r • t_s to the result
    end
end
```

- 其中 r 被称为外部关系（outer relation），s 被称为内部关系（inner relation）
- 这一算法不需要索引就可使用，适用于任何类型的连接条件
- 开销巨大，因为需要检查两个关系中的所有元组对

!!! tip "开销分析"
    在最坏的情况下，如果内存只足够装下每个关系的一个块，那么

    - 块传输次数：$n_r \times b_s + b_r$
        - 外部关系 r 中的所有块都需要读取一次（共 $b_r$ 块）
        - 外部关系 r 中的每个元组（共 $n_r$ 个）都需要遍历一次内部关系 s 的所有块（共 $b_s$ 块）
    - 寻道次数：$n_r + b_r$
        - 每个外部元组都需要重新定位一次内部关系的起始位置（共 $n_r$ 次）
        - 每个外部关系的每个块都需要一次寻道 $b_r$

    如果较小的关系可以完整放入内存中，那么应该把它用作内部关系：

    - 此时的块传递次数将会降为 $b_r + b_s$，寻道次数降为 2 次（两个关系各需要一次寻道）


### Block nested-loop join

块嵌套循环连接是嵌套循环连接的一个变体，它寻找的是数据块对而非是元组对

```python
for each block B_r of r do begin
  for each block B_s of s do begin
    for each tuple t_r in B_r do begin
      for each tuple t_s in B_s do begin
        Check if (t_r, t_s) satisfy the join condition 
        if they do, add t_r • t_s to the result.
      end
    end
  end
end
```

!!! tip "开销分析"
    在最坏的情况下，需要

    - 块传输次数：$b_r \times b_s + b_r$
    - 寻道次数：$2 \times b_r$

    在最理想的情况下，需要

    - 块传输次数：$b_s + b_r$
    - 寻道次数：2

在块嵌套循环中，我们通过以块为单位处理显著减少了对内部关系的重复访问，将 $n_r$ 改进为了 $b_r$（一般而言 $n_r >> b_r$）

!!! note "块嵌套循环的改进"
    假设内存可以容纳 $N$ 个块，那么我们使用 $M-2$ 个来存储外部关系，$1$ 个用于读取内部关系，$1$ 个用作输出缓冲区。

    - 我们可以一次性读取 $M-2$ 个外部关系的块到内存中，并且一次性将 $M-2$ 个外部关系块与内部关系进行连接操作
    - 因此内部关系只需要被扫描 $\lceil b_r / (M-2) \rceil$ 次，而不是 $b_r$ 次

    此时的开销就变为了

    - 块传输次数：$\lceil b_r / (M-2) \rceil \times b_s + b_r$
    - 寻道次数：$2 \times \lceil b_r / (M-2) \rceil$

!!! extra
    我们还可以进行进一步的优化：

    - 如果进行的是等值连接且连接属性是内部关系的键，那么可以在第一次匹配时就终止内部循环
    - 我们可以交替地正向和反向扫描内部循环，以充分利用缓冲区中的块（同时利用 LRU 替换策略）
    - 如果内部关系拥有索引，我们可以用它来加速查找

### Indexed nested-loop join

当内部关系在连接属性上有索引时，我们就可以使用索引嵌套循环连接。

满足两个条件时，我们就可以使用索引来替代文件扫描：

- 连接是等值连接或自然连接
- 在内部关系的连接属性上有可用的索引
    - 必要时我们可以为了此次连接创建一个索引

算法可以被概括为

```python
for each tuple tr in the outer relation r:
  use the index to look up tuples in s that satisfy the join condition with tuple t_r
```

!!! tip "开销分析"
    在最坏的情况下，我们只有一页缓冲区，每个外部关系 r 的中的元组都需要使用索引来查找 s 中对应的元组
    
    - 开销为：$ b_r \times (t_T + t_S) + n_r \times c $
        - 其中 $c$ 是使用索引找到并获取所有匹配的 $s$ 中的元素的成本
        - 我们可以把 $c$ 估计为在连接条件上选择 s 中单个元组的成本
    - 如果两个关系都有索引，就把元素数量较少的作为外关系

### Merge-join

归并连接实际上就是先对两个关系在连接属性上进行排序，然后通过归并操作把两者连接起来。

- 仅适用于等值连接或自然连接
- 每个块只需读取一次（假设给定一个值后，对应的所有元组都能放入内存中）
    
算法的步骤如下：

1. 把两个关系按照连接属性进行排序（假如尚未排序的话）
2. 合并两个排序好的关系，以此来连接它们
    - 连接操作类似于归并排序算法的归并操作，区别主要在于我们要把连接属性相同的元组对保留下来，生成一个连接结果

!!! tip "开销分析"
    - 块传输次数：$b_r + b_s$
    - 寻道次数：$\lceil b_r / b_b \rceil + \lceil b_s / b_b \rceil$
    - 还需要加上对关系进行排序的开销

!!! extra "混合归并连接"
    混合合并连接（Hybrid merge-join）是排序合并连接的一个变种，满足以下条件时我们可以使用
    
    - 一个关系已按连接属性排序
    - 另一个关系在连接属性上有辅助的 B+ 树索引

    算法的步骤如下：

    1. 按顺序扫描排序好的关系，将每个元组与 B+ 树叶子节点上的元组合并（merge）起来
    2. 将上一步的结果按未排序关系的物理地址排序
    3. 按物理地址顺序扫描未排序的关系，并与之前的结果合并，把地址替换为实际元组

### Hash-join

哈希连接利用的是哈希函数会把具有相同连接属性值的元组分到同一个桶中的原理进行连接

- 适用于等值连接或自然连接
- 我们只需要检查同一个同种的元组是否可以连接上即可

我们需要使用同一个哈希函数 $h$ 把关系 $r$ 和 $s$ 划分成 $\{ 0, 1, \cdots, n \}$ 等分区，

- $r_i, s_i$ 分别表示 $r, s$ 中被划分到第 $i$ 部分的元组的集合
- $r_i$ 中的元组只需要与 $s_i$ 中的元组比较即可，因为有可能和它们连接起来的元组一定会在 $s_i$ 中

<figure markdown="span">
    ![](./assets/查询处理7.png){width=55%}
</figure>

!!! note
    - 我们需要选择合适的 $n$ 和哈希函数 $h$，使得所有的 $s_i$ 都一定能放入内存中
    - 通常 $n = \lceil b_s / M \rceil \times f$

        其中 $f$ 为修正因子，通常大约为 1.2

    - 如果发现分区数 $n$ 大于内存可用页数 $M$，那么就需要使用**递归分区**
        - 先使用一个把关系划分为 $M-1$ 份的哈希函数
        - 在使用一个不同的哈希函数对这 $M-1$ 个分区再细分
        - 重复上述操作直到每一个小分区都可以装入内存中位置

!!! tip "开销分析"
    当内存足够、不需要使用递归分区时

    - 块传输次数：$3(b_r + b_s) + 4 \times n$
    - 寻道次数：$2 \times (\lceil b_r / b_b \rceil + \lceil b_s / b_b \rceil) + 2 \times n$
    
    当我们需要使用内存分区时

    - 对 $s$（称为构建关系，build relation）进行划分的次数为 $\lceil \log_{M-1} (b_s) - 1 \rceil$
    - 此时我们最好选择较小的关系作为构建关系
    - 总开销为
        - 块传输次数：$2(b_r + b_s) \times \lceil \log_{M-1} (b_s) - 1 \rceil + b_r + b_s$
        - 寻道次数：$2 \times (\lceil b_r / b_b \rceil + \lceil b_s / b_b \rceil) \times \lceil \log_{M-1} (b_s) - 1 \rceil$

    最理想的情况是整个构建关系都可以放入主存中（不需要划分），开销就降为 $b_r + b_s$ 次块传输和

## Other Operations

### Duplicate elimination

消除重复可以通过排序或哈希实现

- 排序：
    - 首先对关系 $r$ 排序，重复的元组会彼此相邻
    - 当我们进行外部排序的归并操作时，重复的元组不会再次被保留
    - 成本 = 排序的成本（如果关系已经是有序的，就忽略此项）+ 扫描的成本（1 次寻道加上 $b_r$ 次块传输）
- 哈希：
    - 重复的元组会被分配到同一个桶中
    - 我们可以在每个桶中分别检测并删除重复项

### Projection

- 先对每个元组执行一次属性投影
- 然后再对投影结果执行一次重复消除操作

如果我们只投影少量属性，那么投影结果很可能远小于原始关系，这可以加速后续的其他操作

### Aggregation

聚合操作的实现方式与重复消除类似

- 我们可以使用排序或哈希把元组进行分组，然后每一组应用聚合函数
- 当我们在生成顺串和进行中间的归并操作时，可以保留一些可能会使用到的聚合函数中间值
    - 例如对于 count，min，max，sum 等聚合函数，我们可以不断更新元组的最值、数目和总和等
    - 对于 avg，我们可以跟踪 sum 和 count，最后在通过 sum / count 来计算结果

### Set operations

集合操作（$\cup, \cap, -$）可以通过排序后的归并连接的变体或者哈希连接的变体来实现

基于排序后的归并连接：

1. 对两个关系按照相同的搜索键排序
2. 同时扫描两个排序后的关系，执行类似于归并连接的操作
3. 根据集合操作做不同处理
    - 并集（$\cup$）：输出两个关系中的所有元组，但重复元组只输出一次
    - 交集（$\cap$）：只输出同时出现在两个关系中的元组
    - 差集（$-$）：只输出在第一个关系中出现但不在第二个关系中出现的元组

基于哈希连接：

1. 使用相同的哈希函数对两个关系进行划分，得到 $r_1, \cdots, r_n$ 和 $s_1, \cdots, s_n$
2. 对每一个划分结果 $i$，执行下述操作：
    - 把 $r_i$ 加载到内存中后，使用另一个哈希函数为它构建一个内存中的哈希索引
    - 根据集合操作做不同处理
        - 并集（$\cup$）：把 $s_i$ 中不在哈希索引里的元组添加到哈希索引中，最后输出哈希索引中的所有元组
        - 交集（$\cap$）：只有在 $s_i$ 中的元组处于哈希索引中时，才把它输出 
        - 差集（$-$）：对于 $s_i$ 中的每个元组，如果它已经处在哈希索引中了，就把它从索引中删除，最后输出哈希索引中的所有元组

### Outer Join

外连接可以通过以下两种方式计算：

- 先计算内力娜姐，然后在未参与元组属性中填充 null
- 修改连接算法

修改**归并连接**来计算外连接 $r \rtimes s$

- 在 $r \rtimes s$ 中，非参与元组指的是处于 $r - \Pi_R (r \bowtie s)$ 中的元组
- 修改算法内容：在归并过程中，对于来自 $r$ 的每个元组 $t_r$，如果它在 $s$ 中没有匹配的元组，则输出 $t_r$ 填充 null 之后的结果
- 右外部连接和全外部连接计算方法类似

修改**哈希连接**来计算外连接 $r \rtimes s$

- 如果 $r$ 是探测关系，那么就把未匹配的 $r$ 中的元组填充 null 并输出
- 如果 $r$ 是构建关系，那么就在探测阶段跟踪 $r$ 的元组匹配了哪些 $s$ 的元组。在探测结束时，未成功匹配的 $r$ 中的元组填充 null 后再输出
