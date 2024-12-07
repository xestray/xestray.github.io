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
    $$ F(x_1, \cdots, x_n) = P(\xi_1(\omega) \leqslant x_1, \cdots, \xi_n(\omega) \leqslant x_n)
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




## 条件分布


### 离散型




### 连续型




### 一般情况






### 给定随机变量下的条件概率




## 随机变量的函数及其分布



### 离散型随机变量的函数




### 一维连续型随机变量的函数




### 连续性随机向量函数的分布律




