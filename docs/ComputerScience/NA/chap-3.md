---
    comments: true
    statistics: true
---

# Chap 3: Interpolation and Polynomial Approximation

如果一个函数 $y = f(x) $ 的计算过于复杂，甚至这个函数就是未知的，一种近似求解的办法是：首先在一组点 $x_0, \cdots, x_n$ 上计算出函数值 $y_0 = f(x_0), \dots, y_n = f(x_n)$，然后利用这些点构造一个相对简单的函数 $g(x)$ 来对原始函数进行近似。

如果 $g(x)$ 满足对于所有 $i = 0, \cdots, n$，都有 $g(x_i) = f(x_i)$，那么我们称 $g(x)$ 是 $f(x)$ 在这些点上的**插值函数（interpolating function）**。最常见的插值函数形式是代数多项式（algebraic polynomial）

<figure markdown="span">
    ![](./assets/chap-3-1.png){width=75%}
</figure>

## Interpolation and Lagrange Polynomials

形式化地来说，我们的目标是找到一个 $n$ 阶多项式 $P_n(x) = a_0 + a_1 x + a_2 x^2 + \cdots + a_n x^n$，使得对于给定的一组数据点 $(x_0, y_0), (x_1, y_1), \cdots, (x_n, y_n)$，都有
$$ P_n(x_i) = y_i,\quad i = 0, 1, \cdots, n $$

> 注：对任何 $i \neq j$，都有 $x_i \neq x_j$。

**当 $n = 1$ 时**，给定 $(x_0, y_0)$ 和 $(x_1, y_1)$，我们要找到多项式 $P_1(x) = a_0 + a_1 x$，使得 $P_1(x_0) = y_0$ 且 $P_1(x_1) = y_1$。

此时 $P_1(x)$ 就是一个经过这两点的直线函数，即
$$ \begin{aligned}
P_1(x) &= y_0  + \frac{y_1 - y_0}{x_1 - x_0} (x - x_0) \\\\\\\\
&= \frac{x - x_1}{x_0 - x_1} y_0 + \frac{x - x_0}{x_1 - x_0} y_1 \\\\\\\\
&= \sum_{i=0}^{1} L_{1,i}(x) y_i
\end{aligned} $$

这里我们把 $\dfrac{x - x_1}{x_0 - x_1}$ 和 $\dfrac{x - x_0}{x_1 - x_0}$ 分别记为 $L_{1,0}(x)$ 和 $L_{1,1}(x)$，它们被称为**拉格朗日基函数（Lagrange Basis）**。

他们满足 Kronecker Delta 函数：
$$ L_{n,i}(x_j) = \delta_{ij} = \begin{cases}
1, & i = j \\\\
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
    0 = g^{(n+1)}(\xi_x) &= R_n^{(n+1)}(\xi_x) - K(x) (n + 1)! \\\\
    &= [f^{(n+1)}(\xi_x) - P_n^{(n+1)}(\xi_x)] - K(x) (n + 1)! \\\\
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
        |f(x) - P_1(x)| &= \left| \frac{f^{(2)}(\xi)}{2!} (x - x_k)(x - x_{k+1}) \right| \\\\
        &= \left| \frac{e^{\xi}}{2} (x - kh)(x - (k+1)h) \right| \\\\
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
                P_1(x) &= \frac{x - \frac{\pi}{4}}{\frac{\pi}{6} - \frac{\pi}{4}} \cdot \frac{1}{2} + \frac{x - \frac{\pi}{6}}{\frac{\pi}{4} - \frac{\pi}{6}} \cdot \frac{1}{\sqrt{2}} \\\\
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
    f[x_0, x_1, \cdots, x_{k+1}] &= \frac{f[x_1, x_2, \cdots, x_k] - f[x_1, x_1, \cdots, x_{k+1}]}{x_0 - x_{k+1}} \\\\
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
f(x) = f(x_0) + (x-x_0) f[x, x_0] & (1) \\\\
f[x, x_0] = f[x_0, x_1] + (x - x_1) f[x, x_0, x_1] & (2) \\\\
\cdots \\\\
f[x, x_0, x_1, \cdots, x_{n-1}] = f[x_0, x_1, \cdots, x_n] + (x - x_n) f[x, x_0, x_1, \cdots, x_n] & (n+1) 
\end{cases} $$

计算 $(1) + (x-x_0) \times (2) + \cdots + (x-x_0) \cdots (x-x_{n-1}) \times (n+1)$，得到
$$ \begin{aligned}
f(x) &= \textcolor{red}{f(x_0) + f[x_0, x_1] (x-x_0) + f[x_0, x_1, x_2] (x-x_0)(x-x_1) + \cdots} \\\\
&\quad \textcolor{red}{+ f[x_0, x_1, \cdots, x_n] (x-x_0)(x-x_1) \cdots (x-x_{n-1})} \\\\
&\quad + f[x, x_0, x_1, \cdots, x_n] (x - x_0) \cdots (x-x_{n-1})(x - x_n)
\end{aligned} $$

上式中的红色部分就是所想要求出的 $N_n(x)$，而最后一项则是截断误差 $R_n(x)$。不难得到
$$ a_i = f[x_0, x_1, \cdots, x_i],\quad i = 0, 1, \cdots, n $$

!!! note
    - 由于 $n$ 阶的插值多项式是唯一的，因此 $N_n(x)$ 与 $P_n(x)$ 是相同的多项式，只是它们的表达形式不同而已。
    - 他们都有相同的截断误差，即
        $$ \begin{aligned}
        & f[x, x_0, x_1, \cdots, x_n] \omega_{n+1}(x) = \frac{f^{(n+1)}(\xi_x)}{(n+1)!} \omega_{n+1}(x) \\\\
        & \Longrightarrow f[x_0, x_1, \cdots, x_k] = \frac{f^{(k)}(\xi_x)}{k!},\quad \xi_x \in (x_{min}, x_{max})
        \end{aligned} $$

    - Newton 形式的插值多项式在增加新的插值点时，也可以复用之前的计算结果，求解过程与 Neville 方法类似。
        $$ \begin{array}{llllll}
        f(x_0) & & & & & \\\\
        f(x_1) & f[x_0, x_1] & & & & \\\\
        f(x_2) & f[x_1, x_2] & f[x_0, x_1, x_2] & & & \\\\
        \cdots & & & & & \\\\
        f(x_n) & f[x_{n-1}, x_n] & f[x_{n-2}, x_{n-1}, x_n] & \cdots & f[x_0, \cdots, x_n] \\\\
        f(x_{n+1}) & f[x_n, x_{n+1}] & f[x_{n-1}, x_n, x_{n+1}] & \cdots & f[x_1, \cdots, x_{n+1}] & f[x_0, \cdots, x_{n+1}] \\\\
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
        N_n(x) &= N_n(x_0 + th) = \sum_{k=0}^{n} \binom{t}{k} \Delta^k f(x_0) \\\\
        &= f(x_0) + t \Delta f(x_0) + \frac{t(t-1)}{2!} \Delta^2 f(x_0) + \cdots + \frac{t(t-1) \cdots (t - n + 1)}{n!} \Delta^n f(x_0) \\\\
        R_n(x) &= \frac{f^{(n+1)}(\xi_x)}{(n+1)!} t(t-1) \cdots (t-n) h^{n+1},\quad \xi_x \in (x_0, x_n)
        \end{aligned} $$

    - **牛顿后向差分公式（Newton backward-difference formula）**

        把点的顺序颠倒，即计算
        $$ N_n(x) = f(x_n) + f[x_n, x_{n-1}] (x - x_n) + \cdots + f[x_n, x_{n-1}, \cdots, x_0] (x - x_n) \cdots (x - x_1) $$

        令 $x = x_n + th$，则
        $$ N_n(x) = N_n(x_n + th) = \sum_{k=0}^{n} (-1)^k \binom{-t}{k} \nabla^k f(x_n) $$

## Hermite Interpolation

!!! info "密切多项式"
    令 $x_0, x_1, \cdots, x_n$ 是区间 $[a, b]$ 上的 $n + 1$ 个两两不同的点，$m_i$ 是与 $x_i$ 相关联的非负整数，且设 $f \in C^m[a, b]$，其中 $m = \max\limits_{0 \leqslant i \leqslant n} m_i$。那么在 $[a, b]$ 上逼近 $f(x)$ 的密切多项式是为满足以下条件的阶数最小的多项式 $P(x)$：
    $$ \frac{d^k P(x_i)}{dx^k} = \frac{d^k f(x_i)}{dx^k},\quad i = 0, 1, \cdots, n;\ k = 0, 1, \cdots, m_i $$

    具体来说，$P(x)$ 在每个插值点 $x_i$ 处不仅与 $f(x)$ 的函数值相等，而且它们的前 $m_i$ 阶导数值也相等。

    - 相比于之前提到的插值多项式，密切多项还要求导数值也相等，因此会得到更准确、更光滑的插值曲线
    - 密切多项式是一个次数至多为 $\sum\limits_{i=0}^{n} m_i + n$ 的多项式，因为它需要满足 $\sum\limits_{i=0}^{n} (m_i + 1) = \sum\limits_{i=0}^{n} m_i + (n + 1)$ 个条件

我们的目标是找到一个密切多项式，使得对于所有的 $i = 0, 1, \cdots, n$ 和 $k = 0, 1, \cdots, m_i$，都有
$$ P^{(k)}(x_i) = f^{(k)}(x_i) $$

!!! note
    - 给定 $N$ 个条件（即 $N$ 个方程），我们可以唯一确定一个次数不超过 $N - 1$ 的多项式
    - 在一个点 $x_0$ 上与 $f$ 及其前 $m_0$ 阶导数相等的密切多项式就是一个泰勒多项式
        $$ P(x) = f(x_0) + f'(x_0)(x - x_0) + \cdots + \frac{f^{(m_0)}(x_0)}{m_0!} (x - x_0)^{m_0} $$

        且它的余项为
        $$ R(x) = f(x) - \varphi(x) = \frac{f^{(m_0 + 1)}(\xi_x)}{(m_0 + 1)!} (x - x_0)^{m_0 + 1} $$

    - 当对任意的 $i = 0, 1, \cdots, n$，都有 $m_i = 0$ 时，密切多项式就退化为**拉格朗日插值多项式**
    - 当对任意的 $i = 0, 1, \cdots, n$，都有 $m_i = 1$ 时，密切多项式就退化为 **Hermite 插值多项式**

由上可知，Hermite 多项式与其一阶导数与目标函数 $f$ 及其导数 $f'$ 在插值点处分别相等。

??? example "一个特殊的例子"
    假设 $x_0 \neq x_1 \neq x_2$，给定 $f(x_0), f(x_1), f(x_2)$ 以及 $f'(x_1)$，我们希望找到一个多项式 $P(x)$，使得 $P(x_i) = f(x_i),\ i = 0, 1, 2$ 且 $P'(x_1) = f'(x_1)$。

    上面给出了 4 个条件（3 个函数值和 1 个导数值），$P(x)$ 的次数不超过 3。类似于拉格朗日多项式，我们假设 Hermite 多项式可以表示为如下的形式：
    $$ P_3(x) = \sum_{i=0}^{2} f(x_i) h_i(x) + f'(x_1) \hat{h}_1(x) $$

    接着根据已知条件使用待定系数法可以得到
    $$ h_i(x_j) = \delta_{ij},\quad h_i'(x_1) = 0,\quad \hat{h}_1(x_i) = 0,\quad \hat{h}_1'(x_1) = 1 $$

    - $h_0(x)$：有根 $x_1, x_2$，且 $h_0'(x_1) = 0$，因此 $x_1$ 是一个重根，假设它的形式为
        $$ h_0(x) = C_0 (x - x_1)^2 (x - x_2) $$

        代入 $h_0(x_0) = 1$ 可得 $C_0 = \dfrac{1}{(x_0 - x_1)^2 (x_0 - x_2)}$，因此
        $$ h_0(x) = \frac{(x - x_1)^2 (x - x_2)}{(x_0 - x_1)^2 (x_0 - x_2)} $$

    - $h_2(x)$：与 $h_0(x)$ 类似
    - $h_1(x)$：有根 $x_0, x_2$，因此 $h_1(x)$ 的形式为
        $$ h_1(x) = (Ax + b)(x - x_0)(x - x_2) $$

        其中 $A$ 和 $B$ 可以通过 $h_1(x_1) = 1$ 和 $h_1'(x_1) = 0$ 求解得到，具体过程省略。

    - $\hat{h}_1(x)$：有根 $x_0, x_1, x_2$，因此 $\hat{h}_1(x)$ 的形式为
        $$ \hat{h}_1(x) = C_1 (x - x_0)(x - x_1)(x - x_2) $$

        结合 $\hat{h}_1'(x_1) = 1$ 可以求解出 $C_1 = \dfrac{1}{(x_1 - x_0)(x_1 - x_2)}$

    它的误差分析与拉格朗日插值类似，可以得到
    $$ \begin{aligned}
    R_3(x) &= f(x) - P_3(x) \\
    &= K(x) (x - x_0)^2 (x - x_1)^2 (x - x_2),
    \end{aligned} $$

    其中 $K(x) = \dfrac{f^{(4)}(\xi_x)}{4!},\ \xi_x \in (a, b)$。


一般来说，给定 $n + 1$ 个插值点 $x_0, x_1, \cdots, x_n$，和目标函数值 $y_i = f(x_i)$ 以及导数值 $y_i' = f'(x_i)$，Hermite 插值多项式 $H_{2n+1}(x)$ 的阶数不超过 $2n + 1$，并且满足 $H_{2n+1}(x_i) = y_i$ 且 $H'_{2n+1}(x_i) = y_i'$。

!!! note "使用待定系数法求解 Hermite 多项式"
    根据上述条件，并且参考我们刚刚给出的例子，我们可以假设 Hermite 多项式的形式为
    $$ H_{2n+1}(x) = \sum_{i=0}^{n} y_i h_i(x) + \sum_{i=0}^{n} y_i' \hat{h}_i(x) $$

    其中
    $$ \begin{cases}
    h_i(x_j) = \delta_{ij}, & h_i'(x_j) = 0 \\\\
    \hat{h}_i(x_j) = 0, & \hat{h} _ i'(x_j) = \delta _ {ij} \\\\
    \end{cases} $$

    - $h_i(x)$：对应于函数值 $y_i$
        - 它在 $x_j\ (j \neq i)$ 处有根，同时因为 $h_i'(x_j) = 0,\ (j \neq i)$，所以所有的 $x_j$ 都是一个重根，因此它的形式为
            $$ \begin{aligned}
            h_i(x) &= (A_i' x + B_i') \prod_{\substack{j=0 \\ j \neq i}}^{n} (x - x_j)^2 \\\\
            &= (A_i x + B_i) L_{n,i}^2(x)
            \end{aligned} $$

            !!! tip
                注意这里的常系数有所改变，因为 $L_{n,i}^2(x)$ 自身会带有一个额外的系数
                $$ L_{n,i}(x) = \prod\limits_{\substack{j=0 \\ j \neq i}}^{n} \frac{x - x_j}{x_i - x_j} $$

        - $A_i$ 和 $B_i$ 可以通过 $h_i(x_i) = 1$ 和 $h_i'(x_i) = 0$ 求解得到。
        - 最终得到的结果是
            $$ h_i(x) = \left[ 1 - 2 (x - x_i) L _ {n,i}'(x_i) \right] L _ {n,i}^2(x) $$

    - $\hat{h}_i(x)$：对应于导数值 $y_i'$
        - 它在所有的 $x_j$ 处都有根，并且因为 $\hat{h}_i'(x_j) = 0,\ (j \neq i)$，$\hat{h}_i'(x_j) = 1$，所以所有的 $x_j\ (j \neq i)$ 都是一个重根，而 $x_i$ 则是一个单根，因此它的形式为
            $$ \hat{h}_i(x) = C_i (x - x_i) L_{n,i}^2(x) $$

        - 结合 $\hat{h}_i'(x_i) = 1$ 可以求解出 $C_i = 1$
        - 因此最终得到的结果是
            $$ \hat{h} _ i(x) = (x - x_i) L _ {n,i}^2(x) $$

    如果 $a = x_0 < x_1 < \cdots < x_n = b,\quad f \in C^{2n}[a, b]$，那么 Hermite 多项式的误差为
    $$ R_n(x) = f(x) - H_{2n+1}(x) = \frac{f^{(2n+2)}(\xi_x)}{(2n+2)!} \left[ \prod_{i=0}^{n} (x - x_i) \right]^2,\quad \xi_x \in (a, b) $$

## Cubic Spline Interpolation

### Piecewise-polynomial Approximation

最简单的一种分段多项式逼近是分段使用线性逼近，即在每一个子区间 $[x_i, x_{i+1}]$ 上使用线性多项式近似表示 $f(x)$：
$$ f(x) \approx P_1(x) = \frac{x - x_{i+1}}{x_i - x_{i+1}} f(x_i) + \frac{x - x_i}{x_{i+1} - x_i} f(x_{i+1}),\quad x \in [x_i, x_{i+1}] $$
但很显然分段线性逼近得到的函数并不光滑，我们希望使用更高次的多项式来逼近 $f(x)$。

一种可选择的方法是改用 Hermite 插值多项式：如果 $f$ 和 $f'$ 的值在每一个点 $x_i$ 处都是已知的，那么我们可以在每一个子区间 $[x_i, x_{i+1}]$ 上使用 Hermite 三次多项式来逼近 $f(x)$。这样的逼近比较光滑，但是我们需要直到所有的导数值 $f'(x_i)$，这在实际应用中并不总是可行的。

为此，我们可以使用**三次样条插值（cubic spline interpolation）**来逼近 $f(x)$。

### Cubic Spline Interpolation

!!! definition "三次条样插值"
    给定一个定义在 $[a, b]$ 上的函数 $f$ 以及一组节点 $a = x_0 < x_1 < \cdots < x_n = b$。三次样条插值器（cubic spline interpolant）是一个分段三次多项式 $S(x)$，它满足以下条件：

    - $S(x)$ 是一个分段函数，在每一个子区间 $[x_i, x_{i+1}]$ 上都是一个三次多项式，即
        $$ S(x) = S_i(x),\quad x \in [x_i, x_{i+1}],\quad i = 0, 1, \cdots, n-1 $$
    - $S(x_i) = f(x_i),\quad i = 0, 1, \cdots, n$，

        - 即 $S(x)$ 在每一个节点处都与 $f(x)$ 的值相等

    - $S_{i+1}(x_{i+1}) = S_i(x_{i+1}),\quad i = 0, 1, \cdots, n-2$，
    
        - 即 $S(x)$ 在每一个内部节点处都是连续的

    - $S'_{i+1}(x_{i+1}) = S'_i(x_{i+1}),\quad i = 0, 1, \cdots, n-2$，

        - 即 $S(x)$ 在每一个内部节点处的一阶导数都是连续的

    - $S''_{i+1}(x_{i+1}) = S''_i(x_{i+1}),\quad i = 0, 1, \cdots, n-2$，

        - 即 $S(x)$ 在每一个内部节点处的二阶导数都是连续的

    > 后三个条件实际上保证了三次条样插值的光滑性

### Method of Bending Moment

下面我们介绍一种构造三次样条插值的方法：

令 $h_j = x_j - x_{j-1},\ S(x) = S_j(x), \text{for} x \in [x_{j-1}, x_j]$

因为 $S(x)$ 在每一个子区间上都是一个三次多项式，因此 $S_j''(x)$ 是一个一次多项式，由这个区间的两个端点的值决定。假设 $S_j''(x_{j-1}) = M_{j-1},\ S_j''(x_j) = M_j$（$M_j$ 称为节点 $x_j$ 处的弯矩（bending moment）），那么对于 $x \in [x_{j-1}, x_j]$，有
$$ S_j''(x) = M_{j-1} \frac{x_j - x}{h_j} + M_j \frac{x - x_{j-1}}{h_j} $$
积分两次，可以分别得到
$$ S_j'(x) = M_{j-1} \cdot \frac{(x_j - x)^2}{2} + M_j \cdot \frac{(x - x_{j-1})^2}{2h_j} + A_j $$
$$ S_j(x) = M_{j-1} \cdot \frac{(x_j - x)^3}{6h_j} + M_j \cdot \frac{(x - x_{j-1})^3}{6h_j} + A_j x + B_j $$
其中 $A_j$ 和 $B_j$ 是积分常数，可以通过 $S_j(x_{j-1}) = y_{j-1}$ 和 $S_j(x_j) = y_j$ 求解得到：

$$ \begin{aligned}
A_j &= \dfrac{y_j - y_{j-1}}{h_j} - \dfrac{M_j - M_{j-1}}{6} h_j \\
A_j x + B_j &= \left( y_{j-1} - \dfrac{M_{j-1}}{6} h_j^2 \right) \dfrac{x_j - x}{h_j} + \left( y_j - \dfrac{M_j}{6} h_j^2 \right) \dfrac{x - x_{j-1}}{h_j} \\
\end{aligned} $$

现在我们来求解 $M_j$：因为 $S'$ 在 $x_j$ 处连续，因此有

- $[x_{j-1}, x_j]$：
    $$ S_j'(x) = -M_{j-1} \dfrac{(x_j - x)^2}{2h_j} + M_j \dfrac{(x - x_{j-1})^2}{2h_j} + f[x_{j-1}, x_j] - \dfrac{M_j - M_{j-1}}{6}h_j $$

- $[x_j, x_{j+1}]$：
    $$ S_{j+1}'(x) = -M_j \dfrac{(x_{j+1} - x)^2}{2h_{j+1}} + M_{j+1} \dfrac{(x - x_j)^2}{2h_{j+1}} + f[x_j, x_{j+1}] - \dfrac{M_{j+1} - M_j}{6}h_{j+1} $$

又根据 $S_j'(x_j) = S_{j+1}'(x_j)$，我们可以得到 $M_{j-1},\ M_j,\ M_{j+1}$ 之间的关系：

定义 $\lambda_j = \dfrac{h_{j+1}}{h_j + h_{j+1}},\ \mu_j = 1 - \lambda_j$ 以及 $g_j = \dfrac{6}{h_j + h_{j+1}} (f[x_j, x_{j+1}] - f[x_{j-1}, x_j])$

我们就可以得到
$$ \mu_j M_{j-1} + 2M_j + \lambda_j M_{j+1} = g_j $$
其中 $j = 1, 2, \cdots, n-1$。

下图是这个递推式的矩阵表示：

<figure markdown="span">
    ![](./assets/chap-3-5.png){width=55%}
</figure>

我们可以发现我们有 $n-1$ 个方程，但是有 $n+1$ 个未知数 $M_0, M_1, \cdots, M_n$，因此我们还需要额外的两个边界条件来唯一确定这些未知数。

#### 固定边界（clamped boundary）

$$ S'(a) = y_0',\ S'(b) = y_n' $$

- $[a, x_1]$：
    $$ S_1'(x) = -M_0 \dfrac{(x_1 - x)^2}{2h_1} + M_1 \dfrac{(x - a)^2}{2h_1} + f[x_0, x_1] - \dfrac{M_1 - M_0}{6}h_1 $$
- $[x_{n-1}, b]$：
    $$ S_n'(x) = -M_{n-1} \dfrac{(b - x)^2}{2h_n} + M_n \dfrac{(x - x_{n-1})^2}{2h_n} + f[x_{n-1}, x_n] - \dfrac{M_n - M_{n-1}}{6}h_n $$

于是我们额外得到了两个方程：
$$ \begin{cases}
2M_0 + M_1 = \dfrac{6}{h_1} (f[x_0, x_1] - y_0') \triangleq g_0 \\\\
M_{n-1} + 2M_n = \dfrac{6}{h_n} (y_n' - f[x_{n-1}, x_n]) \triangleq g_n
\end{cases} $$

??? note "固定三次条样的伪代码"
    <figure markdown="span">
        ![](./assets/chap-3-6.png){width=70%}
    </figure>

    <figure markdown="span">
        ![](./assets/chap-3-7.png){width=70%}
    </figure>

#### 自然边界（natural boundary）

$$ S''(a) = y_0'' = M_0,\ S''(b) = y_n'' = M_n $$

则有
$$ \begin{aligned}
\lambda_0 &= 0,\quad g_0 = 2 y_0'' \\\\
\mu_n &= 0,\quad g_n = 2 y_n''
\end{aligned} $$

当 $M_0 = M_n = 0$ 时，称为**自然三次条样插值（natural cubic spline interpolation）**，此时 $g_0 = g_n = 0$。

??? note "自然三次条样的伪代码"
    <figure markdown="span">
        ![](./assets/chap-3-8.png){width=70%}
    </figure>

    <figure markdown="span">
        ![](./assets/chap-3-9.png){width=70%}
    </figure>

#### 周期边界

如果 $f$ 是周期函数，即 $y_n = y_0$ 且 $S'(a^+) = S'(b^-)$，那么我们就可以知道 $M_0 = M_n$


!!! property "三次条样的性质"
    - 只要系数矩阵严格对角占优（实际上是确保可逆），那么三次样条就可以由其边界条件唯一确定。
    - 如果 $f \in C[a, b]$ 且 $\dfrac{\max h_i}{\min h_i} \leqslant C < \infty$，那么当 $h_i \rightarrow 0$ 时，$S(x) \xrightarrow{\text{uniform}} f(x)$。
    
        也就是说，在保证不增加样条阶数的情况下，可通过增加节点个数来提升近似精度
