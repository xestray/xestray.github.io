---
    comments: true
    statistics: true
    counter: true
---

# 第二章 随机变量与分布函数

## 离散型随机变量及其分布

### 随机变量的概念

回顾第一章的内容，我们把由直线上一切左开右闭区间和它们经过有限或可列次交、并、逆等运算得到的点集$B$称为（一维）**波雷尔集**，包括有限或无限的开区间、闭区间、半开半闭区间，以及它们的有限个或可列个交、并。并且我们还把波雷尔集的全体 $\mathcal{B}$ 称为**波雷尔域**。

根据上面的说明，我们可以给出随机变量在数学上的严格定义

!!! note "随机变量"
    设 $\xi(\omega)$ 是定义在概率空间 $ \{ \Omega, \mathscr{F}, P \} $ 上的单值实函数，即
    $$ \xi: \Omega \mapsto \mathbb{R} $$
    且对于 $\mathbf{R}$ 上的任一波雷尔集 $B$，有
    $$ \xi^{-1}(B) = \\{ \omega: \xi(\omega) \in B \\} \in \mathscr{F} $$
    即 $\xi(\omega)$ 的任意取值对应的样本点的集合（构成一个事件）都在事件域 $\mathscr{F}$ 中，那么就称 $\xi(\omega)$ 为**随机变量**，称 $ \\{ P(\xi(\omega)) \in B \\}, B \in \mathcal{B} $ 为随机变量 $\xi(\omega)$ 的概率分布

    也就是说，我们把 $\xi$ 看作一个函数，它把一个事件映射到一个实数上去。我们希望当我们对一些事件进行运算时，它的值域 $A(\in R)$ 也能满足这些事件运算的性质，因此我们要求值域是一个波雷尔集，落在波雷尔域 $\mathcal{B}$ 中

### 离散型随机变量

!!! note "离散型随机变量"
    若随机变量 $\xi$ 可能的取值至多可列个（有限个或可列无限个），则称 $\xi$ 为**离散型随机变量** 

对于离散型随机变量，关键在于它的分布列，即它的取值和对应的概率

$$\begin{bmatrix}
        x_1 & x_2 & \cdots & x_n & \cdots \\
        p_1 & p_2 & \cdots & p_n & \cdots
\end{bmatrix}$$

显然，分布列具有性质：

- $p_i > 0, i = 1, 2, \cdots$
- $\sum\limits_{i=1}^{\infty} p_i = 1$

!!! example "常见的离散型随机变量"
    === "退化分布"
        若随机变量 $\xi$ 只取一个值 $x_0$，即 $P(\xi = x_0) = 1$，则称 $\xi$ 服从**退化分布**，也称单点分布。

    === "两点分布"
        $$\begin{bmatrix}
        x_1 & x_2 \\
        p   & q 
        \end{bmatrix}, \enspace p,q > 0, \enspace p+q=1$$

        伯努利分布是一种特殊的两点分布，也称0-1分布

        $$\begin{bmatrix}
        1 & 0 \\
        p & q 
        \end{bmatrix}, \enspace p,q > 0, \enspace p+q=1$$


    === "二项分布"
        $$ P(\xi = k) = \binom{n}{k} p^k q^{n-k}, \enspace p,q > 0, \enspace p+q=1$$
        记作 $\xi \sim B(n,p)$，$n$ 和 $p$ 称为它的两个参数

    === "泊松分布"
        $$ P(\xi = k) = \frac{\lambda^k}{k!}e^{-\lambda}
        ,\lambda>0,\enspace k\in \mathbb{N} $$
        记作 $\xi\sim\mathcal{P}(\lambda)$

        对于二项分布，如果当 $n \to \infty$ 时，有 $np_n \to \lambda$ ，则二项分布趋近于泊松分布。

        记 $\lambda_n = np_n$，则 $p_n = \lambda_n / n$，对于固定的 $k$

        $$\begin{aligned}
        \lim_{n\to\infty} \binom{n}{k} p^k (1-p)^{n-k} &= \frac{n(n-1)\cdots(n-k+1)}{k!} \left( \frac{\lambda_n}{n} \right)^k  \left( 1-\frac{\lambda_n}{n} \right)^{n-k} \\
        &= \frac{\lambda_n^k}{k!} (1-\frac{1}{n}) \cdots (1-\frac{k-1}{n}) \frac{(1-\frac{\lambda_n}{n}) ^ n}{(1-\frac{\lambda_n}{n}) ^ k} \\
        &= \frac{\lambda^k}{k!} e^{-\lambda} \\
        \end{aligned}$$

    === "几何分布"
        $$ P(\xi = k) = p q^{k-1}, \enspace p,q > 0, \enspace p+q=1, \enspace k=1,2,\cdots $$

        常用于解决寻找第一次成功的问题

    === "超几何分布"
        $$ P(\xi = k) = \dfrac{ \dbinom{M}{k} \dbinom{N-M}{n-k} }{ \dbinom{N}{n} }, \enspace n \leqslant N, \enspace M \leqslant N, \enspace k=0,1,\cdots, \min\{n,M\} $$

        常用于解决次品抽样问题，当 $N$ 很大时，超几何分布就可以用二项分布来近似计算


## 分布函数与连续型随机变量





































































