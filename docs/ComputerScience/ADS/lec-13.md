---
    comments: true
    statistics: true
---

# 随机算法

- 确定性算法：给定相同的输入，算法总是产生相同的结果
- 随机算法：给定相同的输入，算法可能产生不能的结果

随机算法通常有以下几个优点：

- 在一些问题中，通过确定性算法来求出最优解可能需要消耗大量的时间和资源，而随机算法可以在一个合理的时间内给出一个接近最优解的结果，进而达到更高的计算效率。
- 随机算法通常比其确定性对应算法更加简洁且易于实现。通过随机化决策，算法的实现往往不需要复杂的逻辑或状态跟踪。例如，某些图算法或图遍历问题，可以通过随机化方法获得高效的解决方案，而不需要依赖复杂的精确计算。
- 对于一些问题，可能没有已知的有效的确定性算法（尤其是 NP 困难问题、NP 完全问题），或者已知的最优算法计算复杂度过高，无法在实际应用中运行。此时随机算法可以提供一种可行的替代方案，通过牺牲一定的精确度来换取更高的效率。

随机算法主要可分为两类：

!!! note "随机算法"
    1. 蒙特卡洛算法（Monte Carlo Algorithm）：efficient randomized algorithms that only need to yield the correct answer with high probability
    
        这种算法在每次运行时都会引入一定的随机性，因此其输出结果可能是不确定的。但是通过多次运行算法，能够在**一定的概率下获得正确的结果**。蒙特卡洛算法通常用于求解一些问题的近似解，或者在无法获得精确解的情况下，通过多次运行算法来提高结果的准确性。它常用于需要进行大量模拟或估算的问题，能够提供一个近似值，但不太需要保证结果一定完全准确。
        
    2. 拉斯维加斯算法（Las Vegas Algorithm）：randomized algorithms that are always correct, and run efficiently in expectation 
    
        这种算法虽然在每次运行时也会引入一定的随机性，但是其**结果总是正确的**。也就是说，拉斯维加斯算法一定会得到一个正确的结果，但是每一次运行的时间可能是不确定的，可能需要多次尝试才能找到正确的解。这类算法特别适合那些对结果准确性要求高，但对执行时间有一定容忍度的应用场景。

## 雇佣问题（Hiring Problem）

!!! question "问题描述"
    假设我们需要从 $n$ 个应聘者中选择其中一个聘用，但是我们不能提前知道每个应聘者的能力，只能通过面试来了解他们的能力。我们每一次只能面试一个应聘者，并且面试成本 $C_i$ 远小于雇佣成本 $C_h$。

    现在我们的目标是找到一个最优的应聘者，即能力最强的应聘者。

对于这个问题，显然我们更关注的是面试和雇佣的总成本，而算法运行的时间，假设我们把所有人都面试了，并且雇佣了其中 $M$ 个应聘者，那么总成本就为 $O(NC_i + MC_h)$。

### 朴素思路

我们可以有一个非常朴素的思路，就是依次面试所有人，假如后一个人比前一个人更优秀，就雇用他。

```C
int Hiring ( EventType C[ ], int N )
{   /* candidate 0 is a least-qualified dummy candidate */
    int Best = 0;
    int BestQ = the quality of candidate 0;
    for ( i=1; i<=N; i++ ) {
        Qi = interview( i ); /* Ci */
        if ( Qi > BestQ ) {
            BestQ = Qi;
            Best = i;
            hire( i );  /* Ch */
        }
    }
    return Best;
}
```

上面这个算法的最坏情况是后一个面试者始终比前一个更优秀，这样一来我们就需要面试 $N$ 次，雇佣 $N$ 个人，总成本为 $O(NC_h)$。

### 随机算法

假如这 $N$ 个面试者以随机顺序被面试，那么前 $i$ 个面试者中的任何一个都可能是目前为止最优秀的，因此根据上面的算法，第 $i$ 个面试者被雇佣的概率为 $\dfrac{1}{i}$。

我们可以定义一个随机变量 $X_i$ 如下：
$$ X_i = \begin{cases} 
1, & \text{第} i \text{个候选者被雇佣} \\\\
0, & \text{第} i \text{个候选者不被雇佣} 
\end{cases} $$

那么总的雇佣次数为 $X = \sum_{i=1}^{N} X_i$，我们可以计算 $E[X]$：
$$ E[X] = E \left[ \sum_{i=1}^{N} X_i \right] = \sum_{i=1}^{N} E[X_i] = \sum_{i=1}^{N} \dfrac{1}{i} = \ln N + O(1) $$

因此总的成本为 $O(C_h \ln N + NC_i)$

因此上面的算法就变成了
```C
int RandomizedHiring ( EventType C[ ], int N )
{   /* candidate 0 is a least-qualified dummy candidate */
    int Best = 0;
    int BestQ = the quality of candidate 0;

    randomly permute the list of candidates;//takes time

    for ( i=1; i<=N; i++ ) {
        Qi = interview( i ); /* Ci */
        if ( Qi > BestQ ) {
            BestQ = Qi;
            Best = i;
            hire( i );  /* Ch */
        }
    }
}
```

显然，这个算法的关键部分在于进行随机排序。我们可以通过下面的算法进行随机排序：

- Assign each element A[i] a random priority P[i], and sort

即给每一个元素都随机生成一个优先级，然后按照这个优先级对它们进行排序

```C
void PermuteBySorting ( ElemType A[ ], int N )
{
    for ( i=1; i<=N; i++ )
    /* makes it more likely that all priorities are unique */
        A[i].P = 1 + rand()%(N^3); 
        
    Sort A, using P as the sort keys;
}
```

### 在线雇佣问题

在在线雇佣问题中，我们仍然是每一次都是能面试一个人，但是我们这一次**只聘用一个人**，并且**每一次面试之后必须立即决定是否聘用被试者**

- 假如聘用，那么后续就不再进行任何面试
- 假如不聘用，那就继续面试下一个人，并且未被选择的人后续也不可以回过头来再聘用

我们可以有如下的算法：
```C
int OnlineHiring ( EventType C[ ], int N, int k )
{
    int Best = N;
    int BestQ = -INFTY ;
    for ( i=1; i<=k; i++ ) {
        Qi = interview( i );
        if ( Qi > BestQ )
            BestQ = Qi;
    }
    for ( i=k+1; i<=N; i++ ) {
        Qi = interview( i );
        if ( Qi > BestQ ) {
            Best = i;
            break;
        }
    }
    return Best;
}
```
 
算法的思路为首先面试前`k`个人，但是都不聘用，记住这`k`个人里面最高的得分，然后从第`k+1`个人开始面试，一旦有人比这个最高分表现更好的人就立即聘用，后续就不再进行面试了。如果一直没有找到表现比前面的最高分更好的人，就聘用最后一个人。

对于这个问题我们需要关注两个方面：

1. 对于给定的`k`，我们能招聘到所有面试者中最好的那一个的概率是多少？
2. `k`取什么值才能使上面这一个概率最大化？

!!! proof
    令 $S_i$ 表示第 $i$ 个面试者是所有面试者中最好的，并且能被我们聘用，那么要使 $S_i$ 成立，需要满足以下两个条件

    - $A$：最好的应聘者在位置 $i$，概率为 $\dfrac{1}{N}$
    - $B$：从位置 $k+1$ 到位置 $i-1$ 没有人被聘用，也就是说前 $i-1$ 个人之中，表现最好的在前 $k$ 个人中，概率为 $\dfrac{k}{i-1}$

    于是事件 $S_i$ 发生的概率为
    $$ \Pr[S_i] = \Pr[A \cap B] = \Pr[A] \cdot \Pr[B] = \dfrac{1}{N} \cdot \dfrac{k}{i-1} = \dfrac{k}{N(i-1)} $$
    我们能雇佣到所有面试者中最好的那一个的概率为
    $$ \Pr[S] = \sum_{i=k+1}^N \Pr[S_i] = \sum_{i=k+1}^N \dfrac{k}{N(i-1)} = \dfrac{k}{N} \sum_{i=k}^{N-1} \dfrac{1}{i} $$

    上式中的连加式的范围被下面的这个积分式控制住了
    $$ \int_k^N \dfrac{1}{x} dx \leqslant \sum_{i=k}^{N-1} \dfrac{1}{i} \leqslant \int_{k-1}^{N-1} \dfrac{1}{x} dx $$

    于是对于给定的`k`，我们能招聘到所有面试者中最好的那一个的概率满足
    $$ \dfrac{k}{N} \ln \left( \dfrac{N}{k} \right) \leqslant \Pr[S] \leqslant \dfrac{k}{N} \ln \left( \dfrac{N-1}{k-1} \right) $$

    我们可以通过简单的求导来找到一个`k`来使这个概率最大
    $$ \dfrac{d}{dk} \left[ \dfrac{k}{N} \ln \left( \dfrac{N}{k} \right) \right] = \dfrac{1}{N} (\ln N - \ln k - 1) = 0  $$
    $$ \Rightarrow k = \dfrac{N}{e} $$

    令 $f(k) = \dfrac{k}{N} \ln \left( \dfrac{N}{k} \right)$，并带入上面的 $k$，我们可以得到
    $$  f(k)_{max} = \dfrac{1}{e} $$

    因此，当 $k = \dfrac{N}{e}$ 时，我们能招聘到所有面试者中最好的那一个的概率至少为 $\dfrac{1}{e}$

## 随机化快速排序

回顾传统的快速排序，我们通常选择一个 pivot，然后将数组分为两部分，小于 pivot 的放在左边，大于 pivot 的放在右边，然后递归的对左右两部分进行递归排序。

但是假如我们每一次选择到的 pivot 都是最大或者最小的元素，就会导致实际上每一次运行都只能减少一个需要排序的元素，就会达到最坏的情况，时间复杂度为 $O(N^2)$。

在平均情况下，快速排序的时间复杂度为 $O(N \log N)$，这需要我们每一次都足够随机地选择 pivot。

为了使得每一个元素都足够随机地被选为 pivot，我们希望这个 pivot 满足下面两个条件

- 中心分割（central splitter）：pivot 可以使得每一侧至少包含 $\dfrac{n}{4}$ 个元素  
- 改进快速排序（Modified Quicksort）：每一次递归之前总是选择一个中心分割的 pivot

<figure>
    <img src="../assets/中心分割.png" width="60%">
</figure>

从上图中可以看出我们选择到一个中心分割的 pivot 的概率为 $\dfrac{1}{2}$，因此从期望的角度来看，我们至多需要选择 2 次才能选到一个中心分割的 pivot。

我们还可以给出下面的定义

- Type $j$：the subproblem $S$ is of type $j$ if $ N \left( \dfrac{3}{4} \right)^{j+1} \leqslant |S| \leqslant N \left( \dfrac{3}{4} \right)^j $

我们可以推出至多有 $\left( \dfrac{3}{4} \right)^{j+1}$ 个属于 type $j$ 的子问题。

对于一个属于特定的 type $j$ 的子问题，我们可以计算其时间复杂度的期望值
$$ E [T_{\text{type } j}] = O(N \left( \dfrac{3}{4} \right)^j) \times \left( \dfrac{3}{4} \right)^{j+1} = O(N) $$
而不同的 type 的总个数为

- Number of different types = $ \log_{4/3} N = O(\log N) $

两者合并就可以得到最终的时间复杂度为 $O(N \log N)$
