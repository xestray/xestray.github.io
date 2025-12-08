---
    comments: true
    statistics: true
---

# Chap 3: Interpolation and Polynomial Approximation

如果一个函数 $y = f(x) $ 的计算过于复杂，甚至这个函数就是未知的，一种近似求解的办法是：首先在一组点 $x_0, \cdots, x_n$ 上计算出函数值 $y_0 = f(x_0), \dots, y_n = f(x_n)$，然后利用这些点构造一个相对简单的函数 $g(x)$ 来对原始函数进行近似。

如果 $g(x)$ 满足对于所有 $i = 0, \cdots, n$，都有 $g(x_i) = f(x_i)$，那么我们称 $g(x)$ 是 $f(x)$ 在这些点上的**插值函数（interpolating function）**。最常见的插值函数形式是代数多项式（algebraic polynomial）

<figure markdown="span">
    ![](./assets/chap-3-1.png){width=85%}
</figure>

## Interpolation and Lagrange Polynomials

形式化地来说，我们的目标是找到一个 $n$ 阶多项式 $P_n(x) = a_0 + a_1 x + a_2 x^2 + \cdots + a_n x^n$，使得对于给定的一组数据点 $(x_0, y_0), (x_1, y_1), \cdots, (x_n, y_n)$，都有

$$ P_n(x_i) = y_i,\quad i = 0, 1, \cdots, n $$

> 注：对任何 $i \neq j$，都有 $x_i \neq x_j$。

**当 $n = 1$ 时**，给定 $(x_0, y_0)$ 和 $(x_1, y_1)$，我们要找到多项式 $P_1(x) = a_0 + a_1 x$，使得 $P_1(x_0) = y_0$ 且 $P_1(x_1) = y_1$。

此时 $P_1(x)$ 就是一个经过这两点的直线函数，即

$$ \begin{aligned}
P_1(x) &= y_0  + \frac{y_1 - y_0}{x_1 - x_0} (x - x_0) \\\\
&= \frac{x - x_1}{x_0 - x_1} y_0 + \frac{x - x_0}{x_1 - x_0} y_1 \\\\
&= \sum_{i=0}^{1} L_{1,i}(x) y_i
\end{aligned} $$

这里我们把 $\dfrac{x - x_1}{x_0 - x_1}$ 和 $\dfrac{x - x_0}{x_1 - x_0}$ 分别记为 $L_{1,0}(x)$ 和 $L_{1,1}(x)$，它们被称为**拉格朗日基函数（Lagrange Basis）**。

他们满足 Kronecker Delta 函数：

$$ L_{n,i}(x_j) = \delta_{ij} = \begin{cases}
1, & i = j \\
0, & i \neq j
\end{cases} $$

接下来我们推广到 **当 $n \geqslant 1$ 时的情况**。

显然，构造出满足条件的 $P_n(x)$ 等价于找到满足条件的 $L_{n,i}(x)$，使得 $P_n(x) = \sum_{i=0}^{n} L_{n,i}(x) y_i$，且 $L_{n,i}(x_j) = \delta_{ij}$。

分析可知，每个 $L_{n,i}(x)$ 都是一个 $n$ 阶多项式，它的根分别为 $x_0, x_1, \cdots, x_{i-1}, x_{i+1}, \cdots, x_n$，因此我们可以将 $L_{n,i}(x)$ 写成如下形式：

$$ L_{n,i}(x) = C_i (x - x_0)(x - x_1) \cdots (x - x_{i-1})(x - x_{i+1}) \cdots (x - x_n) = C_i \prod_{\substack{j = 0 \\ j \neq i}}^n (x - x_j) $$

带入 $L_{n,i}(x_i) = 1$，我们可以解出 $C_i = \prod\limits_{\substack{j = 0 \\ j \neq i}}^n\dfrac{1}{ (x_i - x_j)}$，因此我们得到了 $L_{n,i}(x)$ 的解析表达式：

$$ L_{n,i}(x) = \prod_{\substack{j = 0 \\ j \neq i}}^n \frac{x - x_j}{x_i - x_j} $$

于是我们根据拉格朗日基函数构造出了 $n$ 次的拉格朗日插值多项式：

$$ P_n(x) = \sum_{i=0}^{n} L_{n,i}(x) y_i = \sum_{i=0}^{n} \left( \prod_{\substack{j = 0 \\ j \neq i}}^n \frac{x - x_j}{x_i - x_j} \right) y_i $$

!!! theorem "插值多项式的唯一性"
    设 $x_0, x_1, \cdots, x_n$ 是区间 $[a, b]$ 上的 $n + 1$ 个两两不同的点，则对于任意给定的函数值 $y_0, y_1, \cdots, y_n$，存在且仅存在一个**唯一的** $n$ 次拉格朗日插值多项式 $P_n(x)$，使得对于所有 $i = 0, 1, \cdots, n$，都有 $P_n(x_i) = y_i$。

    !!! proof
        使用反证法。如果存在两个多项式 $P_n(x)$ 和 $Q_n(x)$，都满足插值条件。那么我们可以得到一个新的多项式 $D_n(x) = P_n(x) - Q_n(x)$，它的次数不会超过 $n$，但是它有 $n + 1$ 个不同的根 $x_0, x_1, \cdots, x_n$。
        
        这显然与它的次数不超过 $n$ 矛盾，因此假设不成立，插值多项式是唯一的。

!!! note
    插值多项式不是唯一的，除非它的阶数被约束在不超过 $n$ 的范围内。
    
    也就是说，当我们对 $n$ 个点使用超过 $n$ 阶的多项式进行插值时，可能会有多个不同的多项式满足插值条件。
    
    例如

    $$ P(x) = L_n(x) + p(x) \prod_{i=0}^{n} (x - x_i) $$

    其中 $L_n(x)$ 是 $n$ 次插值多项式，$p(x)$ 是任意的非零多项式。那么 $P(x)$ 也是一个满足插值条件的多项式。

### Analyze the Remainder

下面我们来分析拉格朗日插值法的余项（即分析误差）。

假设 $a \leqslant x_0 < x_1 < \cdots < x_n \leqslant b$，且函数 $f \in C^{n+1}[a, b]$，$P(x)$ 是 $f(x)$ 在 $x_0, \cdots, x_n$ 上的拉格朗日插值多项式，那么对于任意 $x \in [a, b]$，都存在 $\xi \in (a, b)$，使得

$$ R_n(x) = f(x) - P_n(x) = \frac{f^{(n+1)}(\xi)}{(n+1)!} (x - x_0)(x - x_1) \cdots (x - x_n) $$

!!! proof "上式的证明"
    考虑截断误差 $R_n(x) = f(x) - P_n(x)$，显然它在 $x_0, x_1, \cdots, x_n$ 处均为零。因此 $R(x)$ 有至少 $n + 1$ 个不同的根，同时它的次数不超过 $n + 1$，因此 $R_n(x)$ 可以表示为

    $$ R_n(x) = K(x) \prod_{i=0}^{n} (x - x_i) $$

    对于任意一个固定的点 $x \in [a, b]$ 且 $x \neq x_i$，我们定义一个辅助函数（此时 $K(x)$ 是一个常数）

    $$ g(t) = R_n(t) - K(x) \prod_{i=0}^{n} (t - x_i) $$

    则有 $g(x_i) = 0$，$i = 0, 1, \cdots, n$，且 $g(x) = 0$。因此 $g(t)$ 在区间 $[a, b]$ 上至少有 $n + 2$ 个不同的根。

    根据推广的 Rolle 定理，存在 $\xi_x \in (a, b)$，使得 $g^{(n+1)}(\xi_x) = 0$。计算 $g^{(n+1)}(t)$ 可得

    $$ \begin{aligned}
    0 = g^{(n+1)}(\xi_x) &= R_n^{(n+1)}(\xi_x) - K(x) (n + 1)! \\
    &= [f^{(n+1)}(\xi_x) - P_n^{(n+1)}(\xi_x)] - K(x) (n + 1)! \\
    &= f^{(n+1)}(\xi_x) - K(x) (n + 1)! 
    \end{aligned} $$

    > 因为 $P_n(x)$ 是 $n$ 次多项式，所以 $P_n^{(n+1)}(x) = 0$。

    于是我们就可以得到

    $$ K(x) = \frac{f^{(n+1)}(\xi_x)}{(n + 1)!} $$

    截断误差为

    $$ R_n(x) = \frac{f^{(n+1)}(\xi_x)}{(n + 1)!} \prod_{i=0}^{n} (x - x_i) $$

!!! note
    - 因为通常而言 $\xi_x$ 是多少无法确定，因此我们会使用 $f^{(n+1)}(x)$ 的上界来估计余项

        也就是说，寻找一个估计值 $M_{n+1}$，使得对于所有 $x \in [a, b]$，都有 $|f^{(n+1)}(x)| \leqslant M_{n+1}$，因此可以取 $|\ R_n(x)| \leqslant \dfrac{M_{n+1}}{(n + 1)!} \prod_{i=0}^{n} |x - x_i|$ 为总误差的上界

    - 对于任意阶数 $ \leqslant n$ 的多项式函数 $f$ 而言，插值多项式是精确的，因为 $f^{(n+1)}(x) = 0$，因此余项 $R_n(x) = 0$。

??? example 
    === "例 1"

        假设 $x \in [0, 1]$ 的函数 $f(x) = e^x$ 做一个表格，表格中的每一项精确到 8 位小数，步长为 $h$。为使线性插值（即一次的拉格朗日插值）的绝对误差不超过 $10^{-6}$，步长 $h$ 最多应取多大？

        **答：** 
        
        假设把 [0, 1] 分成 $n$ 个等间距的子区间 $[x_0, x_1], \cdot, [x_{n-1}, x_n]$，其中 $x_0 = 0, x_n = 1$。对于 $x \in [x_k, x_{k+1}]$，那么估计误差为：

        $$ \begin{aligned}
        |f(x) - P_1(x)| &= \left| \frac{f^{(2)}(\xi)}{2!} (x - x_k)(x - x_{k+1}) \right| \\
        &= \left| \frac{e^{\xi}}{2} (x - kh)(x - (k+1)h) \right| \\
        &\leqslant \frac{e}{2} \cdot \frac{h^2}{4} = \frac{e h^2}{8}
        \end{aligned} $$

        根据题目要求

        $$ \frac{e h^2}{8} \leqslant 10^{-6} \quad \Longrightarrow \quad h \leqslant \sqrt{\frac{8 \times 10^{-6}}{e}} \approx 1.72 \times 10^{-3} $$

    === "例 2"

        给定 $\sin \dfrac{\pi}{6} = \dfrac{1}{2},\ \sin \dfrac{\pi}{4} = \dfrac{1}{\sqrt{2}},\ \sin \dfrac{\pi}{4} = \dfrac{\sqrt{3}}{2} $ 使用关于 $\sin x$ 的线性以及二次拉格朗日多项式，计算 $\sin 50^\circ$ 并估计误差。（已知 $\sin 50^\circ \approx 0.7660444$）

        **答：** 

        - 线性插值：由于我们有三个点，可以考虑分别使用 $x_0, x_1$ 和 $x_1, x_2$ 来做线性插值。

            1. 使用 $x_0 = \dfrac{\pi}{6},\ x_1 = \dfrac{\pi}{4}$

                $$ \begin{aligned}
                P_1(x) &= \frac{x - \frac{\pi}{4}}{\frac{\pi}{6} - \frac{\pi}{4}} \cdot \frac{1}{2} + \frac{x - \frac{\pi}{6}}{\frac{\pi}{4} - \frac{\pi}{6}} \cdot \frac{1}{\sqrt{2}} \\
                &= \frac{3}{\pi} x + \left( \frac{1}{\sqrt{2}} - \frac{1}{2} \right)
                \end{aligned} $$

                代入 $x = 50^\circ = \dfrac{5\pi}{18}$ 可得

                $$ \sin 50^\circ \approx P_1(\frac{5\pi}{18}) = \frac{5}{6} + \left( \frac{1}{\sqrt{2}} - \frac{1}{2} \right) \approx 0.75985 $$

                因为 $f(x) = \sin x$，所以 $f^{(2)}(x) = -\sin x$，对 $ \xi_x \in (\dfrac{\pi}{6}, \dfrac{\pi}{4})$ 上，$|f^{(2)}(\xi_x)| \in (\dfrac{1}{2}, \dfrac{\sqrt{2}}{2})$

                $$ R_1(x) = \frac{f^{(2)}(\xi_x)}{2!} (x - x_0)(x - x_1) $$

                得到 $-0.01319 < R_1(\dfrac{5\pi}{18}) < -0.00762$

                因此外推误差（Extrapolation Error）$\approx -0.01001$。

            2. 使用 $x_1 = \dfrac{\pi}{4},\ x_2 = \dfrac{\pi}{3}$

                同理可以得到 $\sin 50^\circ \approx 0.76008$，误差范围为 $0.00538 < R_1(\dfrac{5\pi}{18}) < 0.00660$，外推误差 $\approx 0.00596$。

            !!! note
                - 外推（extrapolation）是指在已知数据点的范围之外进行估计
                - 插值（interpolation）则是在已知数据点的范围之内进行估计
                - 一般而言，插值的结果会比外推更准确

        - 二次插值：

            <figure markdown="span">
                ![](./assets/chap-3-2.png){width=85%}
            </figure>

            !!! tip
                通常而言，更高次数的拉格朗日插值会比低次的更加准确，但这未必永远成立。

### Neville's Method

!!! warning "拉格朗日多项式的缺点"
    当我们发现拉格朗日插值法的精度不够时，会考虑使用更多的插值点，但是这样以来新的拉格朗日基函数与先前低次的基函数并不兼容，因此需要重新计算。

    接下来介绍的 Neville 方法可以在增加插值点时，复用之前的计算结果，从而提高计算效率。

!!! definition
    设函数 $f$ 在 $x_0, \cdots, x_n$ 上有定义，并假设 $m_1, \cdots, m_k$ 是 $k$ 个不同的整数，且对于每个 $i$ 都满足 $0 \leqslant m_i \leqslant n$。我们把在 $x_{m_1}, \cdots, x_{m_k}$ 这 $k$ 个点上与 $f(x)$ 具有相同值的拉格朗日多项式记作 $P_{m_1, m_2, \cdots, m_k}(x)$。

!!! theorem
    设函数 $f$ 在 $x_0, \cdots, x_n$ 上有定义，且 $x_i, x_j$ 是其中两个不同的数，那么

    $$ P(x) = \frac{(x - x_j) P_{0, 1, \cdots, j-1, j+1, \cdots,  k}(x) - (x - x_i) P_{0, 1, \cdots, i-1, i+1, \cdots,  k}(x)}{x_i - x_j} $$

    描述了在 $x_0, x_1, \cdots, x_k$ 这 $k+1$ 个点上与 $f(x)$ 具有相同值的 $k$ 次拉格朗日多项式。

    !!! proof
        - 对于任意 $0 \leqslant r \leqslant k$ 且 $r \neq i, r \neq j$，两个在分子上的插值多项式在 $x_r$ 处都分别等于 $f(x_r)$，因此 $P(x_r) = f(x_r)$。
        - 分子上的第一个插值多项式在 $x_i$ 处等于 $f(x_i)$，此时第二个插值多项式等于 $0$，因此 $P(x_i) = f(x_i)$。同理，$P(x_j) = f(x_j)$。
        - 因此我们得到了在 $x_0, x_1, \cdots, x_k$ 这 $k+1$ 个点上对 $f(x)$ 进行插值的 $k$ 阶拉格朗日多项式，根据前面所述，这个多项式是唯一的

基于上述定理，我们知道拉格朗日插值多项式是可以**递归生成的**，例如它们可以按照如下表所示的方法一行一行地生成插值多项式：

$$ \begin{array}{llllll}
x_0 & P_{0} & & & & \\
x_1 & P_{1} & P_{0,1} & & & \\
x_2 & P_{2} & P_{1,2} & P_{0,1,2} & & \\
x_3 & P_{3} & P_{2,3} & P_{1,2,3} & P_{0,1,2,3} & \\
x_4 & P_{4} & P_{3,4} & P_{2,3,4} & P_{1,2,3,4} & P_{0,1,2,3,4} \\
\end{array} $$

这个过程被称作**Neville 方法（Neville's Method）**。

但是 $P$ 的记号显然有些过于笨重和繁琐了，我们可以改用新的记号：

$$ Q_{i, j} = P_{i-j, i-j+1, \cdots, i} $$

其中 $j$ 表示插值多项式的阶数，$i$ 表示插值多项式所使用的最后一个点的下标。因此上面的表格可以改写为：

$$ \begin{array}{llllll}
x_0 & Q_{0, 0} & & & & \\
x_1 & Q_{1, 0} & Q_{1, 1} & & & \\
x_2 & Q_{2, 0} & Q_{2, 1} & Q_{2, 2} & & \\
x_3 & Q_{3, 0} & Q_{3, 1} & Q_{3, 2} & Q_{3, 3} & \\
x_4 & Q_{4, 0} & Q_{4, 1} & Q_{4, 2} & Q_{4, 3} & Q_{4, 4} \\
\end{array} $$

同时上面定理给出的递推公式可以改写为

$$ Q_{i, j} = \frac{(x - x_{i-j}) Q_{i, j-1} - (x - x_i) Q_{i-1, j-1}}{x_i - x_{i-j}} $$

!!! note "Neville 方法的伪代码"
    <figure markdown="span">
        ![](./assets/chap-3-3.png){width=85%}
    </figure>

## Divided Differences

!!! info "差商"
    差商（divided differences）是一个用于表示函数在一组点上的变化率的概念。它们在数值分析中，特别是在插值和数值微分中起着重要作用。

- 1 阶差商：

    $$ f[x_i, x_j] = \frac{f(x_j) - f(x_i)}{x_j - x_i},\ i \neq j $$

- 2 阶差商：

    $$ f[x_i, x_j, x_k] = \frac{f[x_j, x_k] - f[x_i, x_j]}{x_k - x_i},\ i, j, k\ \text{互不相同} $$

- $k+1$ 阶差商：

    $$ \begin{aligned}
    f[x_0, x_1, \cdots, x_{k+1}] &= \frac{f[x_1, x_2, \cdots, x_k] - f[x_1, x_1, \cdots, x_{k+1}]}{x_0 - x_{k+1}} \\
    &= \frac{f[x_1, x_2, \cdots, x_k] - f[x_0, x_1, \cdots, x_{k+1}]}{x_k - x_{k+1}}
    \end{aligned} $$

事实上，

$$ f[x_0, x_1, \cdots, x_k] = \sum_{i=0}^{k} \frac{f(x_i)}{\omega'_{k+1}(x_i)} $$

其中 $\omega_{k+1}(x) = \prod\limits_{i=0}^{k} (x - x_i)$, 且 $\omega'_{k+1}(x_i) = \prod\limits_{\substack{j=0 \\ j \neq i}}^{k} (x_i - x_j)$ 是 $\omega_{k+1}(x)$ 在 $x_i$ 处的导数。

!!! note
    上面这个公式的核心在于 $f[x_0, x_1, \cdots, x_k]$ 的值与 $x_0, x_1, \cdots, x_k$ 的顺序无关。

### Newton’s Interpolation

我们的目标是得到一个 $n$ 阶多项式

$$ N_n(x) = a_0 + a_1 (x - x_0) + a_2 (x - x_0)(x - x_1) + \cdots + a_n (x - x_0) \cdots (x - x_{n-1}) $$

我们将各阶差商依次展开，得到

$$ \begin{cases}
f(x) = f(x_0) + (x-x_0) f[x, x_0] & (1) \\
f[x, x_0] = f[x_0, x_1] + (x - x_1) f[x, x_0, x_1] & (2) \\
\cdots \\
f[x, x_0, x_1, \cdots, x_{n-1}] = f[x_0, x_1, \cdots, x_n] + (x - x_n) f[x, x_0, x_1, \cdots, x_n] & (n+1) \\
\end{cases} $$

计算 $(1) + (x-x_0) \times (2) + \cdots + (x-x_0) \cdots (x-x_{n-1}) \times (n+1)$，得到

$$ \begin{aligned}
f(x) &= \textcolor{red}{f(x_0) + f[x_0, x_1] (x-x_0) + f[x_0, x_1, x_2] (x-x_0)(x-x_1) + \cdots} \\
&\quad \textcolor{red}{+ f[x_0, x_1, \cdots, x_n] (x-x_0)(x-x_1) \cdots (x-x_{n-1})} \\
&\quad + f[x, x_0, x_1, \cdots, x_n](x-x_0) \cdots (x-x_{n-1})(x - x_n)
\end{aligned} $$

上式中的红色部分就是所想要求出的 $N_n(x)$，而最后一项则是截断误差 $R_n(x)$。不难得到

$$ a_i = f[x_0, x_1, \cdots, x_i],\quad i = 0, 1, \cdots, n $$

!!! note
    - 由于 $n$ 阶的插值多项式是唯一的，因此 $N_n(x)$ 与 $P_n(x)$ 是相同的多项式，只是它们的表达形式不同而已。
    - 他们都有相同的截断误差，即

        $$ \begin{aligned}
        & f[x, x_0, x_1, \cdots, x_n] \omega_{n+1}(x) = \frac{f^{(n+1)}(\xi_x)}{(n+1)!} \omega_{n+1}(x) \\
        & \Longrightarrow f[x_0, x_1, \cdots, x_k] = \frac{f^{(k)}(\xi_x)}{k!},\quad \xi_x \in (x_{min}, x_{max})
        \end{aligned} $$

    - Newton 形式的插值多项式在增加新的插值点时，也可以复用之前的计算结果，求解过程与 Neville 方法类似。

        $$ \begin{array}{llllll}
        f(x_0) & & & & & \\
        f(x_1) & f[x_0, x_1] & & & & \\
        f(x_2) & f[x_1, x_2] & f[x_0, x_1, x_2] & & & \\
        \cdots & & & & & \\
        f(x_n) & f[x_{n-1}, x_n] & f[x_{n-2}, x_{n-1}, x_n] & \cdots & f[x_0, \cdots, x_n] \\
        f(x_{n+1}) & f[x_n, x_{n+1}] & f[x_{n-1}, x_n, x_{n+1}] & \cdots & f[x_1, \cdots, x_{n+1}] & f[x_0, \cdots, x_{n+1}] \\
        \end{array} $$

!!! note "Newton 插值差商公式的伪代码"
    <figure markdown="span">
        ![](./assets/chap-3-4.png){width=85%}
    </figure>

### Formulae with Equal Spacing

如果插值节点是等距分布的，即 $x_i = x_0 + i h$，其中 $h$ 是步长，那么我们可以使用差分（differences）来简化计算。

- **前向差分（forward differences）**：

    $$ \Delta f(x_i) = f(x_{i+1}) - f(x_i) $$

    $$ \Delta^k f(x_i) = \Delta^{k-1} f(x_{i+1}) - \Delta^{k-1} f(x_i) $$

- **后向差分（backward differences）**：

    $$ \nabla f(x_i) = f(x_i) - f(x_{i-1}) $$

    $$ \nabla^k f(x_i) = \nabla^{k-1} f(x_i) - \nabla^{k-1} f(x_{i-1}) $$

- **中心差分（central differences）**：

    $$ \delta f(x_i) = f(x_{i+\frac{1}{2}}) - f(x_{i-\frac{1}{2}}) $$

    $$ \delta^k f(x_i) = \delta^{k-1} f(x_{i+\frac{1}{2}}) - \delta^{k-1} f(x_{i-\frac{1}{2}}) $$

    其中 $f(x_{i \pm \frac{1}{2}}) = f(x_i \pm \frac{h}{2})$。

!!! property "Some Important Properties"

    - 线性：$\Delta (a \cdot f(x) + b \cdot g(x)) = a \Delta f(x) + b \Delta g(x)$
    - 如果 $f(x)$ 是 $m$ 阶多项式，那么 $\Delta^k f(x)\ (0 \leqslant k \leqslant m)$ 是 $m-k$ 阶多项式，且 $\Delta^k f(x) = 0\ (k > m)$。
    - 我们还可以通过以下这些函数得到差分的值：
        - $\Delta^n f_k = \sum\limits_{i=0}^{n} (-1)^i \binom{n}{i} f_{n+k-i}$
        - $\nabla^n f_k = \sum\limits_{i=0}^{n} (-1)^{n-i} \binom{n}{i} f_{k+i-n}$
    - 反之，我们也可以从差分中恢复出函数值：
        - $f_{n+k} = \sum\limits_{i=0}^{n} \binom{n}{i} \Delta^i f_k$
    - 我们也可以从差分中得到差商的值
        - $f[x_0, \cdots, x_k] = \frac{\Delta^k f_0}{k! h^k}$
        - $f[x_n, x_{n-1}, \cdots, x_{n-k}] = \frac{\nabla^k f_n}{k! h^k}$
        - 因此结合余项的公式 $R_n(x) = f[x_0, \cdots, x_k] \omega_{n}(x) = \frac{f^{(n)}(\xi_x)}{n!} \omega_{n}(x)$，我们可以得到
            $$ f^{(n)}(\xi_x) = \frac{\Delta^n f_0}{h^n} $$

!!! summary

    牛顿插值的形式为

    $$ N_n(x) = f(x_0) + f[x_0, x_1] (x - x_0) + \cdots + f[x_0, \cdots, x_n] (x - x_0) \cdots (x - x_{n-1}) $$

    - **牛顿前向差分公式（Newton forward-difference formula）**

        令 $x = x_0 + th$，则

        $$ \begin{aligned}
        N_n(x) &= N_n(x_0 + th) = \sum_{k=0}^{n} \binom{t}{k} \Delta^k f(x_0) \\
        &= f(x_0) + t \Delta f(x_0) + \frac{t(t-1)}{2!} \Delta^2 f(x_0) + \cdots + \frac{t(t-1) \cdots (t - n + 1)}{n!} \Delta^n f(x_0) \\
        R_n(x) &= \frac{f^{(n+1)}(\xi_x)}{(n+1)!} t(t-1) \cdots (t-n) h^{n+1},\quad \xi_x \in (x_0, x_n)
        \end{aligned} $$

    - **牛顿后向差分公式（Newton backward-difference formula）**

        把点的顺序颠倒，即计算

        $$ N_n(x) = f(x_n) + f[x_n, x_{n-1}] (x - x_n) + \cdots + f[x_n, x_{n-1}, \cdots, x_0] (x - x_n) \cdots (x - x_1) $$

        令 $x = x_n + th$，则

        $$ N_n(x) = N_n(x_n + th) = \sum_{k=0}^{n} (-1)^k \binom{-t}{k} \nabla^k f(x_n) $$

## Hermite Interpolation






























## Cubic Spline Interpolation
















































