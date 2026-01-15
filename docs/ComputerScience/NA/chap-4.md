---
    comments: true
    statistics: true
---

# Chap 4: Numerical Differentiation and Integration

## Numerical Differentiation

数值微分的目标是对于给定的 $x_0$，求 $f'(x_0)$ 的近似值。

根据微分的定义：$f'(x) = \lim\limits_{h \to 0} \dfrac{f(x+h) - f(x)}{h}$，我们可以使用有限差分来近似导数。

- 前向差分：$f'(x_0) \approx \dfrac{f(x_0 + h) - f(x_0)}{h}$
- 后向差分：$f'(x_0) \approx \dfrac{f(x_0) - f(x_0 - h)}{h}$

<figure markdown="span">
    ![](./assets/chap-4-1.png){width=75%}
</figure>

我们可以构造由插值点 $x_0, x_0 + h$ 确定的拉格朗日多项式来近似表示 $f(x)$：
$$ \begin{aligned}
f(x) &= \dfrac{f(x_0)(x - x_0 - h)}{x_0 - x_0 - h} + \dfrac{f(x_0 + h)(x - x_0)}{x_0 + h - x_0}  + \dfrac{(x - x_0)(x - x_0 - h)}{2} f''(\xi_x) \\\\\\\\
f'(x) &= \dfrac{f(x_0 + h) - f(x_0)}{h} + \dfrac{2(x - x_0) - h}{2} f''(\xi_x) + \dfrac{(x - x_0)(x - x_0 - h)}{2} \cdot \dfrac{d}{dx} [f''(\xi_x)] 
\end{aligned} $$

因此可以得到 $f'(x_0)$ 的近似表达式：
$$ f'(x_0) = \dfrac{f(x_0 + h) - f(x_0)}{h} - \dfrac{h}{2} f''(\xi) $$

类似地，我们可以推广到使用 $n+1$ 个插值点 $\{x_0, x_1, \cdots, x_n\}$ 构造 $n$ 次拉格朗日插值多项式来近似 $f(x)$ 的方法：
$$ \begin{aligned}
f(x) & = \sum\limits_{k=0}^n f(x_k) L_k(x) + \dfrac{(x - x_0) \cdots (x - x_n)}{(n+1)!} f^{(n+1)}(\xi_x) \\\\
f'(x_j) & = \sum\limits_{k=0}^n f(x_k)L_k'(x_j) + \dfrac{f^{(n+1)}(\xi_j)}{(n+1)!} \prod\limits_{\substack{k = 0 \\\\ k \ne j}}^n (x_j - x_k)
\end{aligned} $$

上述导数公式被称为近似 $f'(x_j)$ 的 $n+1$ 点公式。

!!! note
    - 一般来说，插值点越多，近似的精度越高。
    - 但随着插值点的增多，舍入误差也会变大，因此数值微分就也会变得不稳定。

!!! example
    === "例一"
        <figure markdown="span">
            ![](./assets/chap-4-2.png){width=85%}
        </figure>

    === "例二"
        <figure markdown="span">
            ![](./assets/chap-4-3.png){width=85%}
        </figure>

## Elements of Numerical Integration

数值积分的目标是近似计算定积分 $T = \int_a^b f(x) \, dx$ 的值。

但因为 $f(x)$ 的解析表达式可能未知，或者积分计算困难，我们只能通过数值方法来近似计算该积分。一个简单的思路是对 $f(x)$ 的拉格朗日多项式进行积分：从区间 $[a, b]$ 中选取一组不同的点 $a \leqslant x_0 < x_1 \cdots < x_n \leqslant b$。拉格朗日多项式为 $P_n(x) = \sum\limits_{k=0}^n f(x_k) L_k(x)$，因此可以得到：
$$ \int_a^b f(x) dx \approx \sum\limits_{k=0}^n f(x_k) \int_a^b L_k(x) dx $$

为了化简上面这个式子，我们令 $A_k = \int_a^b L_k(x) dx = \int_a^b \prod\limits_{j \neq k} \dfrac{x - x_j}{x_k - x_j} dx$，于是近似的误差为：
$$ \begin{aligned}
R[f] &= \int_a^b f(x) dx - \sum\limits_{k=0}^n A_kf(x_k) \\\\
&= \int_a^b [f(x) - P_n(x)] dx = \int_a^b R_n(x) dx \\\\
&= \int_a^b \dfrac{f^{(n+1)}(\xi_x)}{(n+1)!} \prod\limits_{k=0}^n(x - x_k)dx
\end{aligned} $$

!!! definition "精确度"
    求积公式的精确度 (precision/degree of accuracy) 是指使得求积公式对 $x^k(k = 0, 1, \cdots, n)$ 都精确成立的最大正整数 $n$。

!!! example
    <figure markdown="span">
        ![](./assets/chap-4-4.png){width=80%}
    </figure>

对于一系列等间距的节点 $x_i = a + ih,\ i = 0, 1, \cdots, n$，其中 $h = \dfrac{b-a}{n}$，我们可以推导出一些结论：
$$ \begin{aligned}
A_i & = \int_{x_0}^{x_n} \prod\limits_{j \ne i} \dfrac{x - x_j}{x_i - x_j} dx \\\\
& = \int_0^n \prod\limits_{i \ne j} \dfrac{(t - j)h}{(i - j)h} \times h dt = \dfrac{(b - a)\textcolor{red}{(-1)^{n-i}}}{\textcolor{red}{ni!(n - i)!}} \textcolor{red}{\int_0^n \prod\limits_{i \ne j} (t - j) dt} 
\end{aligned} $$

其中被标为红色的部分被称为**科茨系数（Cotes cofficient）**
$$ C_i^{(n)} = \dfrac{(-1)^{n-i}}{ni!(n - i)!} \int_0^n \prod\limits_{i \ne j} (t - j) dt $$

上面这个公式被称作**牛顿-科茨求积公式（Newton-Cotes Quadrature Formula）**，它的误差为：

!!! note
    科茨系数不取决于 $f(x)$ 或 $[a, b]$，而只取决于 $n$ 和 $i$。因此我们可以把这些系数提前计算好，存储在查找表中以供后续使用。

- $n = 1$
    - $C_0^{(1)} = C_1^{(1)} = \dfrac{1}{2}$
    - $\int_a^b f(x) dx \approx \dfrac{b - a}{2}[f(a) + f(b)]$（称为**梯形法则**，也就是我们看到的上一个 example）
    - 误差为 $R[f] = \int_a^b \dfrac{f''(\xi_x)}{2!}(x-a)(x-b) dx = -\dfrac{1}{12}h^3f''(\xi)$
    - 其中 $\xi \in [a, b], h = \dfrac{b - a}{1}$
    - 精度 = 1
- $n = 2$
    - $C_0^{(2)} = \dfrac{1}{6}, C_1^{(2)} = \dfrac{2}{3}, C_2^{(2)} = \dfrac{1}{6}$
    - $\int_a^b f(x) dx \approx \dfrac{b - a}{6}[f(a) + 4f(\dfrac{a+b}{2}) + f(b)]$（称为**辛普森法则**(Simpson's rule)）
    - $R[f] = -\dfrac{1}{90} h^5 f^{(4)}(\xi)$
    - 其中 $\xi \in (a, b), h = \dfrac{b - a}{2}$
    - 精度 = 3
- $n = 3$：
    - **辛普森 3/8 法则**，精度 = 3
    - $R[f] = -\dfrac{3}{80}h^5 f^{(5)}(\xi)$
- $n = 4$
    - **科茨法则**(Cotes' Rule)，精度 = 5
    - $R[f] = -\dfrac{8}{945}h^7 f^{(6)}(\xi)$

!!! theorem
    对于使用 $n+1$ 个点的牛顿-科茨公式，$\exists \xi \in (a, b)$，使得：

    - 如果 $n$ 为**偶数**，那么 $f \in C^{n+2}[a, b]$ 且
        $$ \int_a ^ b f(x) dx = \sum\limits_{k=0} ^ n A_k f(x_k) + \dfrac{h ^ {n+3}f ^ {(n+2)}(\xi)}{(n+2)!} \int_0 ^ n t ^ 2(t - 1) \cdots (t - n) dt $$

    - 如果 $n$ 为**奇数**，那么  $f \in C^{n+1}[a, b]$
        $$ \int_a^b f(x) dx = \sum\limits_{k=0}^n A_k f(x_k) + \dfrac{h ^ {n+2} f ^ {(n+1)}(\xi)}{(n+1)!} \int_0^n t(t - 1) \cdots (t - n) dt $$

    > 注意：当 $n$ 是偶数时，精度的次数为 $n+1$，精度要高于插值多项式的次数。当 $n$ 是奇数的情况，精度的次数仅为 $n$。

## Composite Numerical Integration

Newton-Cotes 公式以等距节点的插值多项式为基础，但由于高次多项式的震荡行性，当 $n$ 较大时、积分区间较大时，误差会变得很大。为了解决这个问题，我们可以用分段（piecewise）的低次 Newton-Cotes 公式来近似 $f(x)$。

### Composite Trapezoidal Rule

考虑把区间 $[a, b]$ 分成 $n$ 个小区间，每个小区间的长度为 $h = \dfrac{b - a}{n}$，节点为 $x_k = a + kh,\ k = 0, 1, \cdots, n$。在每个小区间 $[x_{k-1}, x_k]$ 上使用梯形法则进行积分近似：
$$ \int_{x_{k-1}}^{x_k} f(x)dx \approx \dfrac{x_k - x_{k-1}}{2} [f(x_{k-1}) + f(x_k)], k = 1, \cdots, n $$

$$ \begin{aligned}
\Longrightarrow \int_a^b f(x)dx &\approx \sum\limits_{k=1}^n \dfrac{h}{2}[f(x_{k-1}) + f(x_k)] \\\\
&= \dfrac{h}{2}\Big[f(a) + 2\sum\limits_{k=1}^{n-1} f(x_k) + f(b) \Big] = \textcolor{cornflowerblue}{T_n}
\end{aligned} $$

误差项为：
$$ R_n[f] = \int_a^b f(x) dx - T_n = -\dfrac{(b - a)h^2}{12} f''(\xi), \quad \xi \in (a, b) $$

### Composite Simpson's Rule

同样把区间 $[a, b]$ 分成 $n$ 个小区间，每个小区间的长度为 $h = \dfrac{b - a}{n}$，节点为 $x_k = a + kh,\ k = 0, 1, \cdots, n$。在每个小区间 $[x_{k-1}, x_k]$ 上使用辛普森法则进行积分近似：
$$ \int_{x_{k-1}}^{x_k} f(x) dx \approx \dfrac{x_k - x_{k-1}}{6} [f(x_{k-1}) + 4f(\dfrac{x_{k-1} + x_k}{2}) + f(x_k)], k = 1, \cdots, n $$

$$
\Longrightarrow \int_a^b f(x)dx \approx \dfrac{h}{6}\Big[f(x_a) + 4\sum\limits_{k=0}^{n-1}f(x_{k+\frac{1}{2}}) + 2\sum\limits_{k=0}^{n-2}f(x_{k+1}) + f(x_b)\Big] = \textcolor{cornflowerblue}{S_n}
$$

$$
R[f] = -\dfrac{b-a}{180} \Big(\dfrac{h}{2}\Big)^4 f^{(4)}(\xi), \quad \xi \in (a, b)
$$

!!! note
    为了简化记号，我们令 $n' = 2n$，那么 $h' = \dfrac{b-a}{n'} = \dfrac{h}{2}, x_k = a+kh'$，所以上面的式子可以写成：
    $$ S_n = \dfrac{h'}{3}[f(a) + 4 \sum\limits_{odd\ k} f(x_k) + 2 \sum\limits_{even\ k} f(x_k) + f(b)] $$

上述的符合方法都是**稳定的**，我们可以看下面这个例子：

!!! example
    考虑在 $[a, b]$ 的 $n$ 个子区间上使用辛普森法则。假设 $f(x_i)$ 由 $f^*(x_i)$ 近似，满足 $f(x_i) = f^*(x_i) + \varepsilon_i\ (i = 0, \cdots, n)$，那么累积误差为：
    $$ e(h) = \Big|\dfrac{h}{3}[\varepsilon_0 + 4 \sum\limits_{odd\ k} \varepsilon_k + 2 \sum\limits_{even\ k} \varepsilon_k + \varepsilon_n]\Big| $$

    若 $|\varepsilon_i| < \varepsilon\ (i = 0, \cdots, n)$，那么：
    $$ e(h) < \dfrac{h}{3} [\varepsilon + 4(n/2)\varepsilon + 2(n/2-1)\varepsilon + \varepsilon] = nh\varepsilon = (b-a)\varepsilon $$

    可以看到，当我们为了提高精确程度而使分区更加精细时，虽然计算量增加了，但是舍入误差与区间数量 $n$ 无关，因此该方法是稳定的。

!!! example "另一个例子"
    <figure markdown="span">
        ![](./assets/chap-4-5.png){width=80%}
    </figure>

## Romberg Integration

回顾梯形法则的误差公式：$R_n[f] = -\dfrac{h^2}{12}(b-a)f''(\xi)$。当我们将每个子区间的长度 $h$ 缩减至原来的一半时，可以得到
$$ R_{2n}[f] = -\Big(\dfrac{h}{2}\Big)^2 \dfrac{1}{12}(b-a)f''(\xi') \approx \dfrac{1}{4}R_n[f] $$

从而可以得到：$\dfrac{I - T_{2n}}{I - T_n} \approx \dfrac{1}{4}$，解得 $I \approx \dfrac{4T_{2n} - T_n}{4 - 1} = \dfrac{4}{3}T_{2n} - \dfrac{1}{3}T_n = S_n$

我们可以注意到这和上面那一个例子中的梯形法则和辛普森法则的关系是一样的。实际上，我们可以通过不断地重复这个过程来得到更高精度的近似值。

一般情况下：

- $\dfrac{4T_{2n} - T_n}{4 - 1} = S_n$
- $\dfrac{4^2S_{2n} - S_n}{4^2 - 1} = C_n$
- $\dfrac{4^3C_{2n} - C_n}{4^3 - 1} = R_n$ 

    这称为 **Romberg 序列**（Romberg sequence）

于是我们可以构造 Romberg 积分表格如下：

<figure markdown="span">
    ![](./assets/chap-4-6.png){width=75%}
</figure>

直观的来说，Romberg 积分就是通过不断地将区间等分为更小的子区间，并使用梯形法则进行积分近似，然后通过 Richardson 外推来提高近似的精度。

!!! note "Romberg 积分的伪代码"
    <figure markdown="span">
        ![](./assets/chap-4-7.png){width=85%}
    </figure>

## Richardson's Extrapolation

Richardson 外推法的目标是使用低阶公式获取高精度的结果。它的核心思想是：利用同一数值方法在不同步长下计算出的结果，通过线性组合来消除或减小近似解中的主要误差项，从而得到一个更高精度的估计值。

假设对于某些 $h \neq 0$，我们有公式 $T_0(h)$，用于近似未知量 $I$，并且其**截断误差**有如下的形式：
$$ T_0(h) - I = \alpha_1 h + \alpha_2 h^2 + \alpha_3 h^3 + \cdots $$

将 $h$ 替换为 $h/2$，我们可以得到另一个近似公式：
$$ T_0(h/2) - I = \alpha_1 (h/2) + \alpha_2 (h/2)^2 + \alpha_3 (h/2)^3 + \cdots $$

结合上面这两个公式，我们就可以把精度从 $O(h)$ 级别提升到 $O(h^2)$ 级别：
$$ \dfrac{2T_0(\frac{h}{2}) - T_0(h)}{2 - 1} - I = -\dfrac{1}{2}\alpha_2 h^2 - \dfrac{3}{4} \alpha_3 h^3 - \cdots $$

$$ T_1 = \dfrac{2T_0(\frac{h}{2}) - T_0(h)}{2 - 1} = I + \beta_1 h^2 + \beta_2 h^3 + \cdots $$

类似地，我们可以将精度提升至 $O(h^3)$ 乃至更高阶：

$$ \begin{aligned}
T_2 &= \dfrac{2^2T_1(\frac{h}{2}) - T_1(h)}{2^2 - 1} = I + \gamma_1 h^3 + \gamma_2 h^4 + \cdots \\\\
T_m &= \dfrac{2^mT_{m-1}(\frac{h}{2}) - T_{m-1}(h)}{2^m - 1} = I + \delta_1 h^{m+1} + \delta_2 h^{m+2} + \cdots
\end{aligned} $$

## Adapative Quadrature Methods

目标：预测函数变化量，根据变化的需求调整步幅。

前面的几种方法是对所有区间递归划分子区间，但这样计算量太大，而且还会产生许多不必要的计算。因此我们可以采取一种仅对具有较大误差的区间进行细分的策略，从而避免对误差已经足够小的区间进行不必要的计算。我们称这样的方法为**自适应求积方法（adapative quadrature mehtods）**。

那么我们就应该能够判断何时误差已经足够小了，不需要再细分区间。一种简单的确定策略是使用 $\int_a^b f(x) dx$ 的总误差 $\varepsilon$ 来确定边界：在长度为 $h$ 的区间中，误差应该应当要小于 $h \dfrac{\varepsilon}{b - a}$。当误差超过这个值时，我们就认为是较大误差，需要继续划分该区间。下方的动图就展示了这种自适应求积方法的思路：

<figure markdown="span">
    ![](./assets/chap-4-8.gif){width=75%}
</figure>

我们也可以考虑下面这个例子：

!!! example
    <figure markdown="span">
        ![](./assets/chap-4-9.png){width=80%}
    </figure>

可以看到，当我们使用 $S\left(a, \dfrac{a+b}{2}\right) + S\left(\dfrac{a+b}{2}, b\right)$ 来近似 $\int_a^b f(x) dx$ 的误差仅有使用 $S\left(a, \dfrac{a+b}{2}\right) + S\left(\dfrac{a+b}{2}, b\right)$ 来近似 $S(a, b)$ 的误差的 $\dfrac{1}{15}$。

## Gaussian Quadrature

目标：构造一个公式 $\int_a^b w(x) f(x) dx \approx \sum\limits_{k=0}^n A_k f(x_k)$，使得只需要 $n+1$ 个节点 $x_k$ 就能达到 $2n+1$ 的精度。

思路：确定 $2n+2$ 个未知量 $x_0, \cdots, x_n;\ A_0, \cdots, A_n$，使得公式在 $f(x) = 1, x, x^2, \cdots, x^{2n+1}$ 上都是精确的。点 $x_0, \cdots, x_n$ 被称为**高斯点**(Gaussian points)，这个方法被称为**高斯求积**(Gaussian quadrature)

!!! example 
    使用 $n = 1$ 的高斯求积近似表示 $\int_0^1 \sqrt{x} f(x) dx$。

    **答：** 假设 $\int_0^1 \sqrt{x} f(x) dx \approx A_0 f(x_0) + A_1 f(x_1)$

    那么这个公式必须在 $f(x) = 1, x, x^2, x^3$ 上都是精确的，于是得到
    $$ \begin{cases}
    \frac{2}{3} = A_0 + A_1 \\\\
    \frac{2}{5} = A_0 x_0 + A_1 x_1 \\\\
    \frac{2}{7} = A_0 x_0^2 + A_1 x_1^2 \\\\
    \frac{2}{9} = A_0x_0^3 + A_1 x_1^3
    \end{cases} $$
    
    解得：
    $$ \begin{cases}
    x_0 \approx 0.8212 \\\\
    x_1 \approx 0.2899 \\\\
    A_0 \approx 0.3891 \\\\
    A_1 \approx 0.2776
    \end{cases} $$

!!! theorem
    当且仅当 $W(x) = \prod\limits_{k=0}^n(x - x_k)$ 与所有阶数不超过 $n$ 的多项式正交时，$x_0, \dots, x_n$ 是**高斯点**。

??? proof
    - 若 $x_0, \dots, x_n$ 是高斯点，则公式 $\int_a^b w(x) f(x) dx \approx \sum\limits_{k=0}^nA_kf(x_k)$ 的精度至少为 $2n+1$。那么对于任意多项式 $P_m(x)\ (m \leqslant n)$，$P_m(x) W(x)$ 的阶数不超过 $2n+1$。因此上述公式对于 $P_m(x) W(x)$ 而言是精确的，也就是说：
        $$ \int_a^b w(x) P_m(x) W(x) dx = \sum\limits_{k=0}^n A_k P_m(x_k) W(x_k) = 0 $$

    - 要证明 $x_0, \dots, x_n$ 是高斯点，我们需要证明公式对任意多项式 $P_m(x)\ (m \leqslant 2n + 1)$ 是精确的。令 $P_m(x) = W(x) q(x) + r(x)$，那么 
        $$ \begin{aligned}
        \int_a^n w(x) P_m(x) dx & = \int_a^n w(x) W(x) q(x) dx + \int_a^n w(x) r(x) dx = \sum\limits_{k=0}^n A_k r(x_k) \\\\
        & = \sum\limits_{k=0}^n A_k P_m(x_k) 
        \end{aligned} $$

换句话说，我们希望找到一组 $[a, b]$ 上的正交多项式序列 $\{ \varphi_0, \varphi_1, \cdots, \varphi_n, \cdots \}$，并且 $\varphi_n$ 的阶数为 $n$，那么根据正交的性质我们可以知道，$\varphi_{n+1}$ 一定与任何阶数不超过 $n$ 的多项式正交。

> 因为阶数小于等于 $n$ 的多项式一定在以 $\{ \varphi_0, \varphi_1, \cdots, \varphi_n \}$ 为基展开的空间中，而 $\varphi_{n+1}$ 一定与该空间中的任意元素正交。

因此我们就可以选择 $\varphi_{n+1}(x) = \prod\limits_{k=0}^n (x - x_k)$ 作为权重函数 $W(x)$，此时 $\varphi_{n+1}(x)$ 的根 $x_0, x_1, \cdots, x_n$ 就是我们需要的高斯点，选择这些点可以使得高斯求积公式的代数精度达到理论最大值 $2n+1$。

!!! example
    <figure markdown="span">
        ![](./assets/chap-4-10.png){width=80%}
    </figure>

    <figure markdown="span">
        ![](./assets/chap-4-11.png){width=80%}
    </figure>

还有一些特殊的正交多项式族，当我们选择它们的根作为高斯点时，可以得到一些特殊的高斯求积公式（它们的内积都是通常意义上在区间 $[a, b]$ 上的函数内积）：
$$ \langle f, g \rangle = \int_a^b f(x) g(x) w(x) dx $$


- **勒让德多项式**(Legendre polynomials)：定义在 $[-1, 1]$ 上且 $w(x) \equiv 1$
    - 多项式定义为
        $$ P_k(x) = \dfrac{1}{2^k k!} \dfrac{d ^ k}{dx ^ k}(x ^ 2 - 1) ^ k $$

    - 勒让德多项式之间的内积关系为：
        $$ \langle P_k, P_l \rangle = 
        \begin{cases}0 & k \neq l \\\\ 
        \dfrac{2}{2k+1} & k = l
        \end{cases} $$

    根据 $P_0 = 1, P_1 = x$，我们可以得到递推关系
    $$ (k + 1)P_{k+1} = (2k + 1)xP_k - kP_{k-1} $$

    使用 $P_{n+1}(x)$ 的根作为高斯点，可以得到**高斯-勒让德求积公式**（Gauss-Legendre Quadrature Formula）：

- **切比雪夫多项式**(Chebyshev polynomials)：定义在 $[-1, 1]$ 上且 $w(x) = \dfrac{1}{\sqrt{1 - x^2}}$
    - 多项式定义为
        $$ T_k(x) = \cos(k \cdot \arccos x) $$

    - $T_{n+1}(x)$ 的根为 $x_k = \cos\left(\dfrac{2k + 1}{2n + 2} \pi\right), k = 0, 1, \cdots, n$
    
    使用这些点作为高斯点，可以得到**高斯-切比雪夫求积公式**（Gauss-Chebyshev Quadrature Formula）：
        $$ \int_{-1}^1 \dfrac{f(x)}{\sqrt{1 - x^2}} dx \approx \dfrac{\pi}{n + 1} \sum\limits_{k=0}^n f\left[\cos\left(\dfrac{2k + 1}{2n + 2} \pi\right)\right] = \sum\limits_{k=0}^n A_k f(x_k) $$

    我们可以注意到积分端点 $x = \pm 1$ 是积分上的奇点，此时在等间距的点上使用牛顿-科茨公式会出现问题，而使用高斯积分公式可以避免这个问题。