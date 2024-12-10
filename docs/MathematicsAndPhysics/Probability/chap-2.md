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

### 分布函数

离散型随机变量用分布列来表示其概率分布，但其他随机变量不具有分布列，例如一个随机变量可取的值为某区间内的任意值，此时就无法一一罗列这些值及其概率，因此我们引入新的方法来描述概率分布，并且我们希望这个描述方法能够对一切的随机变量都适用。

!!! definition "分布函数"
    设 $\xi$ 是一个随机变量，对任意实数 $x$，定义函数 $F(x)$ 为
    $$ F(x) = P(\xi \leqslant x) $$
    则称 $F(x)$ 为随机变量 $\xi$ 的**分布函数**。对于一个给定的随机变量，其分布函数是唯一确定的，它是实变量 $x$ 的函数。

    分布函数具有以下性质：
    - $F(x)$ 是一个单调不减函数：若 $x_1 < x_2$，则 $F(x_1) \leqslant F(x_2)$
    - 有界性：$0 \leqslant F(x) \leqslant 1$
    - $\lim\limits_{x \to -\infty} F(x) = 0, \lim\limits_{x \to +\infty} F(x) = 1$
    - $F(x)$ 是右连续的，$F(x+0) = F(x)$

    为了方便起见，我们常把$\lim\limits_{x \to -\infty} F(x)$ 和 $\lim\limits_{x \to +\infty} F(x)$ 简写为 $F(-\infty)$ 和 $F(+\infty)$

!!! tip
    - 对于离散型随机变量，其分布函数是阶梯函数
    - 对于连续型随机变量，其分布函数是连续函数，可以利用密度函数的积分来计算
    - 只要某个函数具有上面的几条性质，那么它就一定可以作为某个随机变量的分布函数

### 连续型随机变量及密度函数

!!! note "连续型随机变量"
    若随机变量 $\xi$ 可取某个区间（有限或无限）中的一切值，并且存在某个非负的可积函数 $p(x)$，使得分布函数 $F(x)$ 满足
    $$ F(x) = \int_{-\infty}^{x} p(t) \mathrm{d}t , \quad -\infty < x < \infty $$
    则称 $\xi$ 为**连续型随机变量**，函数 $p(x)$ 称为 $\xi$ 的**概率密度函数**，简称为**密度函数**。

连续性随机变量的分布函数具有以下良好的数学性质：

!!! property "连续性随机变量的分布函数的性质"
    - F(x) 在 $p(x)$ 的连续处可导

        $$ F'(x) = p(x) $$

    - 只要给出密度函数 $p(x)$，就可以求出 $\xi$ 落在区间 $(a,b]$ 内的概率

        $$ \begin{aligned}
        P(a < \xi \leqslant b) &= F(b) - F(a) \\
        &= \int_{-\infty}^{a} p(x) \mathrm{d}x - \int_{-\infty}^{b} p(x) \mathrm{d}x \\
        &= \int_{a}^{b} p(x) \mathrm{d}x
        \end{aligned} $$

        由此对于 $\mathbb{R}$ 上的一切波雷尔集 $B$，都可以通过 $p(x)$ 来计算概率
        $P(\xi \in B) = \int_{B} p(x) \mathrm{d}x , \quad B \in \mathcal{B}$

    - 对于任一常数 $c$，有 
    
        $$ P(\xi = c) = F(c) - F(c-0) = \lim_{h \to o^+} \int_{c-h}^c p(x) \mathrm{d}x = 0 $$

密度函数还具有如下性质：

- 非负性：$$ p(x) \geqslant 0 $$
- 规范性：$$ \int_{-\infty}^{+\infty} p(x) \mathrm{d}x = 1 $$

### 常见的连续型随机变量

#### 均匀分布

对 $a < b$，称随机变量 $\xi$ 服从区间 $[a,b]$ 上的**均匀分布（uniform distribution）**，如果它的密度函数为

$$ p(x) = 
\begin{cases}
    \dfrac{1}{b-a}, & a \leqslant x \leqslant b \\
    0, & \text{其他}
\end{cases} $$

简记作 $\xi \sim U(a,b)$，其分布函数为

$$ F(x) =
\begin{cases}
    0, & x < a \\
    \dfrac{x-a}{b-a}, & a \leqslant x < b \\
    1, & x \geqslant b
\end{cases} $$

#### 正态分布

若随机变量 $\xi$ 的密度函数为
$$ p(x) = \dfrac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x-a)^2}{2\sigma^2}} $$
则称 $\xi$ 服从参数为 $a$ 和 $\sigma^2$ 的**正态分布（normal distribution）**，也称**高斯分布**，简记作 $\xi \sim N(a,\sigma^2)$

正态分布的分布函数无法用初等函数表示，但可以用积分表达式表示
$$ F(x) = \dfrac{1}{\sqrt{2\pi}\sigma} \int_{-\infty}^{x}  e^{-\frac{(t-a)^2}{2\sigma^2}} \mathrm{d}t $$

- 正态分布的密度函数的图像是一个钟形曲线，关于直线 $x=a$ 对称，即 $p(x) = p(2a-x)$，且在 $x=a$ 处取得最大值 $\dfrac{1}{\sqrt{2\pi}\sigma}$，
- 正态函数的数学期望为 $a$，方差为 $\sigma^2$

!!! note "标准正态分布"
    当 $a=0$，$\sigma=1$ 时，则称 $\xi$ 服从**标准正态分布（standard normal distribution）**，记作 $\xi \sim N(0,1)$

    此时的密度函数为
    $$ p(x) = \dfrac{1}{\sqrt{2\pi}} e^{-\frac{x^2}{2}} $$

    如果 $\xi$ 是服从正态分布 $N(a,\sigma^2)$ 我们可以通过如下的变换来将其转化为标准正态分布
    $$ \eta = \dfrac{\xi - a}{\sigma} \sim N(0,1) $$

!!! info "3$\sigma$原则"
    对于一般的正态分布 $\xi \sim N(a,\sigma^2)$，令 $\eta = \dfrac{\xi - a}{\sigma}$，则有

    $$ P(|\xi - a| \leqslant \sigma) = P(|\eta| \leqslant 1) \approx 0.6827 $$

    $$ P(|\xi - a| \leqslant 2\sigma) = P(|\eta| \leqslant 2) \approx 0.9545 $$

    $$ P(|\xi - a| \leqslant 3\sigma) = P(|\eta| \leqslant 3) \approx 0.9973 $$

    这说明正态随机变量的值几乎都落在 $（a - 3\sigma, a + 3\sigma）$ 区间内，在实际应用中，我们就认为不可能落在这个区间之外。

#### 指数分布

指数分布的密度函数为
$$ p(x) = \begin{cases}
\lambda e^{-\lambda x}, & x \geqslant 0 \\
0, & x < 0
\end{cases} $$
其中 $\lambda > 0$，称 $\xi$ 服从参数为 $\lambda$ 的**指数分布（exponential distribution）**，记作 $\xi \sim E(\lambda)$

指数分布的分布函数为
$$ F(x) = \begin{cases}
1 - e^{-\lambda x}, & x \geqslant 0 \\
0, & x < 0
\end{cases} $$

指数分布的数学期望为 $\dfrac{1}{\lambda}$，方差为 $\dfrac{1}{\lambda^2}$

指数分布具有类似于几何分布的无记忆性

$$ \begin{aligned}
P(\xi > s+t | \xi > s) &= \dfrac{P(\xi > s+t, \xi > s)}{P(\xi > s)} \\
&= \dfrac{ e^{-\lambda (s+t)} }{ e^{-\lambda s} } \\
&= e^{-\lambda t} = P(\xi > t)
\end{aligned} $$

还可以证明，指数分布是具有上述性质的唯一的连续型分布

#### 其他常见的几种连续性随机变量

常见的随机变量可以从这里查阅到。

=== "$\Gamma$ 分布"
    若随机变量 $\xi$ 的密度函数为

    $$ p(x) = \begin{cases}
    \dfrac{\lambda^r}{\Gamma(r)} x^{r-1} e^{-\lambda x}, & x \geqslant 0 \\\\
    0, & x < 0
    \end{cases} $$

    其中 $\Gamma(r)$ 是第一型欧拉积分，则称 $\xi$ 服从参数为 $\lambda$ 和 $r$ 的**$\Gamma$ 分布**，记作 $\xi \sim \Gamma(\lambda, r)$

    当 $r$ 为整数时也称爱尔兰分布，特别地，当 $r = 1$ 时，$\Gamma$ 分布就是指数分布

=== "$\beta$ 分布"
    若随机变量 $\xi$ 的密度函数为

    $$ p(x) = \begin{cases}
    \dfrac{1}{B(a,b)} x^{a-1} (1-x)^{b-1}, & 0 \leqslant x \leqslant 1 \\\\
    0, & \text{其他}
    \end{cases} $$

    则称 $\xi$ 服从参数为 $a$ 和 $b$ 的**$\beta$ 分布**，记作 $\xi \sim \beta(a, b)$。

    其中 $a,b>0$，$B(a,b) = \int_0^1 x^{a-1} (1-x)^{b-1} \mathrm{d}x$ 是 $\beta$ 积分，
    $$ B(a,b) = \dfrac{\Gamma(a+b)}{\Gamma(a)\Gamma(b)} $$

=== "柯西分布"
    柯西分布的密度函数为
    $$ p(x) = \dfrac{1}{\pi} \dfrac{1}{1+(x-\theta)^2}, \quad -\infty < x < +\infty $$
    其中参数 $-\infty < \theta < +\infty $   

=== "卡方分布"
    卡方分布是多个独立标准正态分布的平方和的分布，记作 $\xi \sim \chi^2(n)$，其密度函数为

    $$ p(x) = \begin{cases}
    \dfrac{1}{2^{n/2}\Gamma(n/2)} x^{n/2-1} e^{-x/2}, & x \geqslant 0 \\\\
    0, & x < 0
    \end{cases} $$
    其中 $n$ 为自由度
     
## 随机向量

!!! definition "随机向量"
    若随机变量 $\xi_1(\omega), \xi_2(\omega), \cdots, \xi_n(\omega)$ 都定义在同一个概率空间上，则称 $\boldsymbol{\xi} = \left(\xi_1, \xi_2, \cdots, \xi_n \right)$ 为 $n$ 维**随机向量**，或 $n$ **维随机变量**，简称**随机向量**

### 离散型随机向量

如果随机向量只能取有限组或可列组值，就称它为离散型随机向量。对于离散型随机向量，我们同样可以用分布列来描述其概率分布。

一般地，离散型的二维联合分布列为
$$ P(\xi = x_i, \eta = y_j) = p_{ij}, \quad i,j = 1,2,\cdots $$
记作 $p(x_i, y_j) = p_{ij}$

$n$ 维离散性向量的联合分布是
$$ P(\xi_1 = x_{i_1}, \xi_2 = x_{i_2}, \cdots, \xi_n = x_{i_n}) = p(x_{i_1}, x_{i_2}, \cdots, x_{i_n}) = p_{i_1i_2\cdots i_n} $$
其中 $i_1, i_2, \cdots, i_n = 1,2,\cdots$

对于上式，一定满足如下性质：

$$ \sum_{i_1} \sum_{i_2} \cdots \sum_{i_n} p_{i_1i_2\cdots i_n} = 1 $$

$$ p_{i_1i_2\cdots i_n} \geqslant 0 $$

!!! note "边际分布"
    以二维离散型随机向量 $(\xi, \eta)$ 为例，$\xi$ 和 $\eta$ 的边际分布列分别为
    $$ p_{i\cdot} = P(\xi = x_i) = \sum_{j} p_{ij}, \quad i = 1,2,\cdots $$
    $$ p_{\cdot j} = P(\eta = y_j) = \sum_{i} p_{ij}, \quad j = 1,2,\cdots $$

    对于离散型随机向量，它的边际分布就是将联合分布列中的另一个变量固定，对另一个变量求和得到的分布列

!!! warning 
    当联合分布给定时，边际分布唯一确定；但反过来，边际分布给定时，联合分布未必唯一确定，还需要考虑各个分量之间的关系

### 分布函数

!!! definition "随机向量的分布函数"
    对任意的 $(x_1, \cdots, x_n) \in \mathbb{R}$，称 $n$ 元函数
    $$ F(x_1, \cdots, x_n) = P(\xi_1(\omega) \leqslant x_1, \cdots, \xi_n(\omega) \leqslant x_n) $$
    为随机向量 $\xi(\omega) = \left( \xi_1(\omega), \cdots, \xi_n(\omega) \right)$ 的联合分布函数

联合分布函数具有如下性质：

!!! property
    - 对每个变量单调不减
    - 对每个变量右连续
    - 对任意 $(x,y)$

        $$ F(x, -\infty) = 0, \quad F(-\infty, y), \quad F(\infty, \infty) $$

    - 因为概率一定不小于零，因此对于任意实数 $a_1 < b_1, a_2 < b_2$ 有

        $$ F(b_1, b_2) - F(a_1, b_2) - F(b_1, a_2) + F(a_1, a_2) \geqslant 0 $$

### 连续型随机向量

若存在 $n$ 元可积的非负函数 $p(x_1, \cdots, x_n)$，使 $n$ 元分布函数可表示为
$$ F(x_1, \cdots, x_n) = \int_{-\infty}^{x_1} \cdots \int_{-\infty}^{x_n} p(y_1, \cdots, y_n) \mathrm{d}y_1 \cdots \mathrm{d}y_n $$

则称它是连续型分布，并称 $p(x_1, \cdots, x_n)$ 为（联合）密度函数。显然密度函数具有如下的性质：

- $p(x_1, \cdots, x_n) \geqslant 0$
- $$ \int_{-\infty}^{\infty} \cdots \int_{-\infty}^{\infty} p(y_1, \cdots, y_n) \mathrm{d}y_1 \cdots \mathrm{d}y_n = 1$$
- $$ \dfrac{ \partial^n F(x_1, \cdots, x_n) }{ \partial x_1 \cdots \partial x_1 } = p(x_1, \cdots, x_n) $$

!!! note "边际分布"
    仍以二维随机向量 $(\xi, \eta)$ 为例，$\xi$ 和 $\eta$ 的边际分布列分别为

$$ \begin{aligned}
F_{\xi}(x) &= F(x,\infty) = \int_{-\infty}^x \int_{-\infty}^{\infty} p(u,v) du dv \\\\
&= \int_{-\infty}^x \left[ \int_{-\infty}^{\infty} p(u,v) dv \right] dv
\end{aligned} $$

令 
$$ p_{\xi}(u) = \int_{-\infty}^{\infty} p(u,v) dv $$
则
$$ F_{\xi}(x) = \int_{-\infty}^x p_{\xi}(u) du $$

因此 $p_{\xi}(u)$ 就是 $\xi$ 的密度函数，同理，$\eta$ 的密度函数为
$$ p_{\eta}(v) = \int_{-\infty}^{\infty} p(u,v) du $$


## 随机向量的独立性

!!! note
    === "离散型"

        如果离散型随机向量 $(\xi, \eta)$ 的联合分布列满足
        $$ P(\xi = x_i, \eta = y_j) = P(\xi = x_i) P(\eta = y_j) ,\quad i,j = 1,2,\cdots $$
        则称 $\xi$ 和 $\eta$ 是**相互独立**的，否则称 $\xi$ 和 $\eta$ 是**相依**（或**相关**）的

        上式也可以写成
        $$ p_{ij} = p_{i\cdot} p_{\cdot j} $$

    === "连续型"

        设 $p(x,y)$，$p_{\xi}(x)$ 和 $p_{\eta}(y)$ 分别是连续型随机向量 $(\xi, \eta)$ 的联合分布函数及其边际分布函数，则 $\xi$ 和 $\eta$ 相互独立的充要条件是对任意 $x,y$ 有
        $$ p(x,y) = p_{\xi}(x) p_{\eta}(y) $$

对于任意类型的随机变量 $\xi$ 和 $\eta$，它们相互独立意味着它们的联合分布可以分解为各自的边际分布的乘积，即
$$ F(x,y) = F_{\xi}(x) F_{\eta}(y) $$

类似地，我们也可以把上面的几个式子推广到 $n$ 维随机向量的情况：

1. 随机变量 $\xi_1, \xi_2, \cdots, \xi_n$ 相互独立的充要条件是对一切一维波雷尔点集 $B_1, B_2, \cdots, B_n$ 有
    $$ P(\xi_1 \in B_1, \cdots, \xi_n \in B_n) = P(\xi_1 \in B_1) \cdots P(\xi_n \in B_n) $$
2. $n$ 维随机向量 $\boldsymbol{\xi}$ 与 $m$ 维随机向量 $\boldsymbol{\eta}$ 相互独立被定义为
    $$ P(\boldsymbol{\xi} \in A, \boldsymbol{\eta} \in B) = P(\boldsymbol{\xi} \in A) P(\boldsymbol{\eta} \in B) $$
    其中 $A,B$ 分别是任意的 $n$ 维和 $m$ 维波雷尔点集
3. 若随机变量 $\boldsymbol{\xi}$ 和 $\boldsymbol{\eta}$ 相互独立，那么它们各自的子向量也相互独立

## 条件分布

### 离散型

我们称
$$ P(\eta = y_j | \xi = x_i) = \dfrac{P(\xi = x_i, \eta = y_j)}{P(\xi = x_i)} = \dfrac{p_{ij}}{p_{i\cdot}}, \quad j=1,2,\cdots $$
为在 $\xi = x_i$ 的条件下，$\eta$ 的**条件概率分布列**，简称为条件分布，记为 $p_{\eta | \xi}(y_j | x_i)$。称
$$ P(\eta \leqslant y_j | \xi = x_i) = \sum_{j:y_j \leqslant y}^{j} p_{\eta | \xi}(y_j | x_i) $$
为在 $\xi = x_i$ 的条件下，$\eta$ 的**条件分布函数**，记为 $F_{\eta | \xi}(y | x_i)$

### 连续型

设随机向量 $(\xi, \eta)$ 的联合密度函数为 $p(x,y)$，$\xi$ 有边际密度函数 $p_{\xi}(x) = \int_{-\infty}^{\infty} p(x,y) dy$。若在 $x$ 处，$p_{\xi}(x) > 0$，则称
$$ P(\eta \leqslant y | \xi = x) = \int_{-\infty}{y} \dfrac{p(x,y)}{p_{\xi}(x)} dv, \quad y \in \mathbb{R} $$
为在 $\xi = x$ 的条件下，$\eta$ 的**条件分布函数**，记为 $F_{\eta | \xi}(y | x)$。称
$$ p_{\eta | \xi}(y | x) = \dfrac{p(x,y)}{p_{\xi}(x)} $$
为在 $\xi = x$ 的条件下，$\eta$ 的**条件密度函数**

同理，我们也有
$$ p_{\xi | \eta}(x | y) = \dfrac{p(x,y)}{p_{\eta}(y)}, \quad x \in \mathbb{R} $$

!!! tip
    由条件密度公式可得
    $$ p(x,y) = p_{\xi | \eta}(x | y) p_{\eta}(y) $$
    从而
    $$ p_{\eta | \xi}(y | x) = \dfrac{p_{\xi | \eta}(x | y) p_{\eta}(y) }{\int_{-\infty}^{\infty}p_{\xi | \eta}(x | v) p_{\eta}(v) dv } $$

    这可以看作贝叶斯公式的连续型形式

### 一般情况

对于一般的随机变量 $(\xi, \eta)$，对于给定的 $x$，我们称下面的极限为在 $\xi = x$ 的条件下，$\eta$ 的条件分布函数

$$ \begin{aligned}
F_{\eta | \xi}(y | x) &= \lim_{\epsilon \to 0} \dfrac{P(\eta \leqslant y | x-\epsilon < \xi \leqslant x+\epsilon )}{P(x-\epsilon < \xi \leqslant x+\epsilon)} \\\\
& = \lim_{\epsilon \to 0} \dfrac{F(y, x+\epsilon) - F(y, x-\epsilon)}{F_{\xi}(x+\epsilon) - F_{\xi}(x-\epsilon)}
\end{aligned} $$

这里要求对于任意的 $y \in \mathbb{R}$ 上面的极限都存在。

!!! note "全概率公式的连续形式"
    $$ \begin{aligned}
    P(A) = P(A, -\infty < X < \infty) &= \int_{-\infty}^{\infty} P(A | X = x) p_{X}(x) dx \\\\
    &= \int_{-\infty}^{\infty} g(x) p_{X}(x) dx
    \end{aligned} $$

    其中 $g(x) = P(A | X = x)$ 就是 $A$ 在 $X = x$ 的条件概率密度函数

## 随机变量的函数

!!! definition "波雷尔函数"
    设 $g(x)$ 是一维实函数，$\mathcal{B}$ 是 $\mathbb{R}$ 上的波雷尔 $\sigma$-域。若对于任意 $B \in \mathcal{B}$，有

    $$ \{x: g(x) \in B \} = g^{-1}(B) \in \mathcal{B} $$

    即波雷尔集的原相集也是波雷尔集，则称 $g(x)$ 是一维波雷尔函数。

    我们也可以上面的定义推广到 $n$ 维。

对于一个函数 $f$ 和 $Y = f(X)$，如果 $X$ 是随机变量，我们希望 $Y$ 也是一个随机变量，就要满足 $f$ 是一个波雷尔函数。对于一维的情况，我们通常有以下两种方式来计算 $Y$ 的分布：

- $X$ 是离散型：此时 $Y$ 也是离散型随机变量。我们可以根据 $X$ 的取值和分布列来得到 $Y$ 的取值，进而得到 $Y$ 的分布列

    $$ P(Y = y_j) = P(f(X) = y_j) = P(X = x_i) = p_i $$

- $X$ 是连续型：此时 $Y$ 不一定是连续型随机变量，并不存在一个统一的计算公式。

    但是我们通常可以先求出 $Y$ 的分布函数 $F_Y(y)$，然后再求导得到 $Y$ 的密度函数 $p_Y(y)$

!!! theorem 
    假设 $f(x)$ 严格单调，其反函数 $f^{-1}(x)$ 有连续导函数，则 $\eta = f(\xi)$ 也是连续型随机变量，其密度函数为
    
    $$ g(y) = \begin{cases}
    p(f^{-1}(y)) \left| \left( f^{-1}(y) \right)' \right|, & y \in f(x) \text{的值域} \\
    0, & \text{其他}
    \end{cases} $$

!!! lemma
    假设 $y = f(x)$ 在不相重叠的区间 $I_1, I_2, \cdots$ 上逐段严格单调，且 $I_i, i=1,2,\cdots$ 之和为 $(-\infty, \infty)$，在各段的反函数 $h_1(y), h_2(y),\cdots$ 有连续导数，则 $\eta = f(\xi)$ 是连续型随机变量，其密度为

    $$ g(y) = \begin{cases}
    \sum p(h_i(y)) \left| h'_i(y) \right|, & y \in \text{各} h_i(x) \text{的定义域} \\
    0, & \text{其他}
    \end{cases} $$

上面这个引理告诉我们，如果 $f(x)$ 在不相重叠的区间上逐段严格单调，那么我们可以将 $f(x)$ 的密度函数分段求和得到 $g(y)$ 的密度函数。下面来看一个简单的例子。

!!! example
    设 $\xi \sim N(0,1)$，求 $\eta = \xi^2$ 的密度函数

    **解**： $y=x^2$ 是分段单调的，它的反函数为：

    - 当 $x \in I_1 = (-\infty, 0)$ 时，$x = h_1(y) = -\sqrt{y}$
    - 当 $x \in I_2 = (0, \infty)$ 时，$x = h_2(y) = \sqrt{y}$

    它们的值域都是 $y > 0$，故对于$y > 0$，有

    $$ \begin{aligned}
    g_{\eta}(y) &= \dfrac{1}{\sqrt{2\pi}} \exp\left\{-\dfrac{(-\sqrt{y})^2}{2}\right\} \cdot \left| \dfrac{-1}{2\sqrt{y}} \right| \, + \, \dfrac{1}{\sqrt{2\pi}} \exp\left\{-\dfrac{(\sqrt{y})^2}{2}\right\} \cdot \left| \dfrac{1}{2\sqrt{y}} \right| \\\\
    &= \dfrac{1}{\sqrt{2\pi y}} \, e^{-\frac{y}{2}}
    \end{aligned} $$

    当 $y \leqslant 0$ 时，$g_{\eta}(y) = 0$

    这个分布被称为卡方分布，记作 $\eta \sim \chi^2(1)$

## 随机向量的函数

假设 $(\xi_1,\xi_2,\cdots,\xi_i)$ 是连续型随机向量，具有联合密度函数 $p(x_1,x_2,\cdots,x_i)$，令 $\eta = f(\xi_1,\xi_2,\cdots,\xi_i)$ 则 $\eta$ 的分布函数可以由以下公式得到

$$ \begin{aligned}
F_{\eta}(y) &= P(f(\xi_1,\cdots,\xi_i) \leqslant y)\\
            &= \int \cdots \int_{f(\xi_1,\xi_2,\cdots,\xi_i) \leqslant y} p(x_1,x_2,\cdots,x_i)dx_1\cdots dx_i
\end{aligned} $$

下面我们来讨论一下常见的并且值得注意的例子

!!! example

    === "$\eta=\xi_1+\xi_2$"
        我们仅考虑 $\xi_1$ 和 $\xi_2$ 是连续型随机变量的情况

        $$ \begin{aligned}
        F_{\eta}(y) &= \iint\limits_{x_1+x_2 \leqslant y} p(x_1,x_2) dx_1 dx_2 \\\\
                    &= \int_{-\infty}^{\infty} dx_1\int_{-\infty}^{y-x_1} p(x_1,x_2)dx_2
        \end{aligned} $$

        作变量代换 $x_2 = z - x_1$，再交换积分次序，得

        $$ \begin{aligned}
        F_{\eta}(y) &= \int_{-\infty}^{\infty} dx_1\int_{-\infty}^{y} p(x_1,z - x_1) dx_z \\\\
                    &= \int_{-\infty}^{y} dz\int_{-\infty}^{\infty} p(x_1,z-x_1)dx_1
        \end{aligned} $$

        这说明 $\eta$ 是连续型随机变量，密度函数为
        $$ p_{\eta}(y) = \int_{-\infty}^{\infty} p(x,y-x)dx $$

        特别地，当 $\xi_1$ 和 $\xi_2$ 相互独立时，各自有密度函数 $p_1(x)$ 和 $p_2(x)$时，$\eta$ 的密度函数为
        $$ p_{\eta}(y) = \int_{-\infty}^{\infty} p_1(x)p_2(y-x)dx  = \int_{-\infty}^{\infty} p_1(y-x)p_2(x)dx $$
        这个式子称为卷积公式

        相应地，$\eta=\xi_1 - \xi_2$ 得密度函数为
        $$ p_{\eta}(y) = \int_{-\infty}^{\infty} p_1(x)p_2(y+x)dx $$
        需要注意的是，这里被积分部分的两个式子不能交换

    === "$\eta = \xi_1 \xi_2$"
        分布函数为

        $$ \begin{aligned}
        F_{\eta}(z) &= \int_{(x, y) : xy \leqslant z} p(x, y) \, dx \, dy \\\\
        &= \int_{-\infty}^0 \int_{z/x}^{\infty} p(x, y) \, dy \, dx + \int_0^{\infty} \int_{-\infty}^{z/x} p(x, y) \, dy \, dx \\\\
        &= -\int_{-\infty}^z \int_{-\infty}^0 \frac{1}{x} p\left(x, \frac{y}{x}\right) \, dx \, dy + \int_0^{\infty} \int_0^z \frac{1}{x} p\left(x, \frac{y}{x}\right) \, dx \, dy
        \end{aligned} $$

        密度函数为

        $$ \begin{aligned}
        p_{\eta}(z) &= -\int_{-\infty}^0 \frac{1}{x} p\left(x, \frac{z}{x}\right) \, dx + \int_0^{\infty} \frac{1}{x} p\left(x, \frac{z}{x}\right) \, dx \\\\
        &= \int_{-\infty}^{\infty} \frac{1}{|x|} p\left(x, \frac{z}{x}\right) \, dx
        \end{aligned} $$

        当 $\xi_1$ 和 $\xi_2$ 相互独立时，密度函数为
        $$ p_{\eta}(z) = \int_{-\infty}^{\infty} \frac{1}{|x|} p_1(x)p_2\left(\frac{z}{x}\right) \, dx $$

    === "$\eta=\dfrac{\xi_1}{\xi_2}$"
        分布函数为

        $$ \begin{aligned}
        F_{\eta}(y) &= P \left( \frac{\xi_1}{\xi_2} \leqslant y \right) = \iint_{x_1 / x_2 \leqslant y} p(x_1, x_2) \, dx_1 \, dx_2 \\\\
        &= \int_{-\infty}^0 \int_{y x_2}^{\infty} p(x_1, x_2) \, dx_1 \, dx_2 + \int_0^{\infty} \int_{-\infty}^{y x_2} p(x_1, x_2) \, dx_1 \, dx_2 \\\\
        \end{aligned} $$

        令 $x_1 = z x_2$ 并交换积分次序，得

        $$ F_{\eta}(y) = \int_{-\infty}^y \int_{-\infty}^{\infty} p(z x_2, x_2) x_2 \, dx_2 \, dz = \int_{-\infty}^y p_{\eta}(z) \, dz $$

        这说明若 $(\xi_1, \xi_2)$ 是连续型随机向量，则 $ \eta = \frac{\xi_1}{\xi_2} $ 是连续型随机变量，其密度函数为

        $$ p_{\eta}(z) = \int_{-\infty}^{\infty} p(z x, x) |x| \, dx $$

    === "次序统计量的分布"
        设 $ \xi_1, \xi_2, \dots, \xi_n $ 独立同分布，分布函数都为 $F(x) $。把 $ \xi_1, \xi_2, \dots, \xi_n$ 每取一组值 $\xi_1(\omega), \xi_2(\omega), \dots, \xi_n(\omega) (\omega \in \Omega)$ 都按大小次序排列，所得随机变量 $\xi_1^*, \xi_2^*, \dots, \xi_n^*$ 称为 $\xi_1, \xi_2, \dots, \xi_n$ 的**次序统计量**，它们满足 $\xi_1^* \leqslant \xi_2^* \leqslant \dots \leqslant \xi_n^*$。
        
        按定义，$\xi_1^* = \min(\xi_1, \xi_2, \dots, \xi_n)$，$\xi_n^* = \max(\xi_1, \xi_2, \dots, \xi_n)$

        现在我们来求 $\xi_1^*, \, \xi_n^*$ 及 $(\xi_1^*,\xi_n^*)$ 的分布，这在数理统计中是有用的。

        1. **$\xi_n^*$ 的分布函数**

            $$ \begin{aligned}
            P(\xi_n^* \leqslant x) &= P(\xi_1 \leqslant x, \xi_2 \leqslant x, \cdots, \xi_n \leqslant x) \\
            &= P(\xi_1 \leqslant x) P(\xi_2 \leqslant x) \cdots P(\xi_n \leqslant x) \\
            &= [F(x)]^n
            \end{aligned} $$

        2. **$\xi_1^*$ 的分布函数**

            首先考虑 $\{ \xi_1^* \leqslant x \}$ 的逆事件 $\{ \xi_1^* > x \}$，即 $\xi_1, \xi_2, \dots, \xi_n$ 中至少有一个大于 $x$，则

            $$ \begin{aligned}
            P(\xi_1^* > x) &= P(\xi_1 > x, \xi_2 > x, \cdots, \xi_n > x) \\
            &= P(\xi_1 > x) P(\xi_2 > x) \cdots P(\xi_n > x) \\
            &= [1 - F(x)]^n
            \end{aligned} $$

            从而
            $$ P(\xi_1^* \leqslant x) = 1 - P(\xi_1^* > x) = 1 - [1 - F(x)]^n $$

        3. **$(\xi_1^*,\xi_n^*)$ 的联合分布函数**

            $$ \begin{aligned}
            F(x,y) &= P(\xi_1^* \leqslant x, \xi_n^* \leqslant y) \\
            &= P(\xi_n^* \leqslant y) - P(\xi_1^* > x, \xi_n^* \leqslant y) \\
            &= [F(y)]^n - P\Big(\bigcap_{i=1}^n (x < \xi_i < y)\Big)
            \end{aligned} $$

            因此当 $x < y$ 时
            $$ F(x,y) = [F(y)]^n - [F(y) - F(x)]^n $$
            当 $x \geqslant y$ 时
            $$ F(x,y) = [F(y)]^n $$

            如果 $\xi_1, \cdots, \xi_n$ 是连续型随机变量，有密度 $p(x) = F'(x)$，则上面各随机变量（向量）也是连续型的，可将各分布函数求导以得到密度函数

!!! tip
    上面的从负无穷积分到正无穷的写法只是形式上的，实际上的积分区域要根据密度函数的具体取值来判断

### 随机向量的变换

我们仅考虑连续型随机向量的变换。

假设 $(X,Y)$ 为连续型随机向量，具有联合密度函数 $p_{(X,Y)}(x,y)$。变换如下：

$$ \begin{cases}
U = f_1(X,Y) \\
V = f_2(X,Y)
\end{cases} $$

我们要求 $(U,V)$ 的分布。基本方法为

$$ \begin{aligned}
P(U \leqslant u, V \leqslant v) &= P(f_1(X,Y) \leqslant u, f_2(X,Y) \leqslant v) \\
&= P((X,Y) \in D_{uv}) \\\\
&= \int_{D_{uv}} p(x,y) \, dxdy
\end{aligned} $$

其中 $D{uv} = \{ (x,y) : f_1(x,y) \leqslant u, f_2(x,y) \leqslant v \}$

如果 $f_1$ 和 $f_2$ 存在逆变换 $g_1$ 和 $g_2$，则有

$$ \begin{cases}
X = g_1(U,V) \\
Y = g_2(U,V)
\end{cases} $$

进一步假设 $g_1$ 和 $g_2$ 可导，可得到 Jacobi 行列式

$$ J = \begin{vmatrix}
\dfrac{\partial g_1}{\partial u} & \dfrac{\partial g_1}{\partial v} \\\\
\dfrac{\partial g_2}{\partial u} & \dfrac{\partial g_2}{\partial v}
\end{vmatrix} $$

那么 $(U,V)$ 是连续型随机变量，具有联合密度函数 $p_{(U,V)}(u,v)$，有
$$ P(U \leqslant u', V \leqslant v') = \int_{(u,v):u \leqslant u', v \leqslant v'} p(g_1(u,v), g_2(u,v)) |J| \, dudv $$

即
$$ p_{(U,V)}(u,v) = p(g_1(u,v), g_2(u,v)) \, |J| $$

!!! tip
    关键的思路在于首先把 $x,y$ 使用 $u,v$ 表示，然后再乘上从 $u,v$ 转变到 $x,y$ 的 Jacobi 行列式

我们也可以把上面二维的情况推广到 $n$ 维。

!!! note "$n$ 维随即向量的变换"
    设 $(\xi_1, \cdots, \xi_n)$ 的密度函数为 $p(x_1, \cdots, x_n)$，现有 $m$ 个函数：$\eta_1 = f_1(x_1, \cdots, x_n), \cdots, \eta_m = f_m(x_1, \cdots, x_n)$，则 $(\eta_1, \cdots, \eta_m)$ 也是随机向量。

    除了各边际分布之外，我们要求联合分布，联合分布函数为

    $$ \begin{aligned}
    G(y_1, \cdots, y_m) &= P(\eta_1 \leqslant y_1, \cdots, \eta_m \leqslant y_m) \\\\
    &= \int \cdots \int_D p(x_1, \cdots, x_n) dx_1 \cdots dx_n
    \end{aligned} $$

    这里 $D$ 是 $n$ 维区域：$D = \{ (x_1, \cdots, x_n) : f_1(x_1, \cdots, x_n) \leqslant y_1, \cdots, f_m(x_1, \cdots, x_n) \leqslant y_m \}$

    如果 $m = n, f_j, j = 1, \ldots, n$ 有唯一的反函数组: $x_i = x_i(y_1, \ldots, y_n), i = 1, \ldots, n$, 且

    $$ J = \frac{\partial(x_1, \ldots, x_n)}{\partial(y_1, \ldots, y_n)} \neq 0 $$

    则 $(\eta_1, \ldots, \eta_n)$ 是连续型随机变量. 当 $(y_1, \ldots, y_n) \in (f_1, \ldots, f_n)$ 的值域时，其密度为

    $$ q(y_1, \ldots, y_n) = p(x_1(y_1, \ldots, y_n), \ldots, x_n(y_1, \ldots, y_n)) \, |J| $$

    在其他情况下 $q(y_1, \ldots, y_n) = 0$

!!! example
    $\xi, \eta$ 相互独立，并且都服从参数为1的指数分布，求 $\alpha = \xi + \eta, \beta = \xi / \eta$ 的联合密度函数，并分别求出 $\alpha, \beta$ 的边际密度函数

    **解**：由题意可知 $\xi, \eta$ 的联合密度为：当 $x > 0$ 且 $y > 0$ 时
    $$ p(x,y) = e^{-(x+y)} $$
    其他情况密度函数为0

    函数组 $\begin{cases} u = x + y \\ v = x / y \end{cases}$ 的反函数组为 $\begin{cases} x = \dfrac{uv}{1+v} \\\\ y = \dfrac{u}{1+v} \end{cases}$，

    当 $x,y > 0$，$u,v > 0$ 时，容易计算

    $$ \begin{align*}
    J^{-1} &= \dfrac{\partial(u,v)}{\partial(x,y)} = \begin{vmatrix} 1 & 1 \\ \frac{1}{y} & -\frac{x}{y^2} \end{vmatrix} \\\\
    &= -\dfrac{x+y}{y^2} = -\dfrac{(1+u)^2}{u}
    \end{align*} $$

    故
    $$ |J| = \dfrac{u}{(1+u)^2} $$

    于是 $(\alpha, \beta)$ 的联合密度函数为

    $$ q(u,v) = \begin{cases}
    \dfrac{u e^{-u}}{(1+u)^2}, & u > 0, v > 0 \\\\
    0, & \text{其他}
    \end{cases} $$

    编辑密度分别为

    $$ p_{\alpha}(u) = \begin{cases}
    u e^{-u}, & u > 0 \\\\
    0, & u \leqslant 0
    \end{cases} $$

    $$ p_{\beta}(v) = \begin{cases}
    \dfrac{1}{1 + v^2}, & v > 0 \\\\
    0, & v \leqslant 0
    \end{cases} $$

    并且 $\alpha, \beta$ 相互独立

    !!! abstract
        上面这个例子告诉我们：

        - 要判断随机变量的几个函数是否独立，可用随机变量变换求得它们的联合分布，再用独立性的各种必要条件来判断
        - 要求随机变量的一个函数的分布，有时可作适当补充，先求它们的联合分布，而后要求的函数的分布则作为其边际分布

        如果我们只知道 $\alpha = \xi + \eta$，并且要求它的分布，我们可以将其补全为 $(\alpha,\beta) = (\xi + \eta, \eta)$，再计算 $(\alpha,\beta)$ 的联合分布，最后求出 $\alpha$ 的边际分布。可以发现最后得到的结果与上面的例子是一样的
