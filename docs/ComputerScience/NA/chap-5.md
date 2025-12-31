---
    comments: true
    statistics: true
---

# Chap 5: Initial-Value Problems for Ordinary Differential Equations

## The Elementary Theory of Initial-Value Problems

1 阶常微分方程（ordinary differential equations, ODE）的初值问题（initial-value problem, IVP）的一般形式为：
$$ \begin{cases} 
\dfrac{dy}{dt} = f(t, y),\ t \in [a, b] \\\\
y(a) = \alpha
\end{cases} $$

> 第一个方程表示未知函数 $y$ 关于自变量 $t$ 的导数是一个关于 $t$ 和 $y$ 的已知函数 $f$
> 第二个方程表示在初始时刻 $t=a$ 时，未知函数 $y$ 取值为 $\alpha$。

目标：在一组网格点(mesh points) $a = t_0 < t_1 < \cdots < t_n = b$ 上（通常是等间距的）计算 $y(t)$ 的近似值。也就是说，计算近似值 $w_i \approx y(t_i) = y_i\ (i = 1, \cdots, n)$。

!!! definition "Lipschitz 条件"
    对于函数 $f(t, y)$，若存在常数 $L > 0$，对于定义中任意两点 $(t, y_1),\ (t, y_2) \in D$，均满足不等式
    $$ |f(t, y_1) - f(t, y_2)| \leqslant L|y_1 - y_2| $$

    我们称该函数满足在变量 $y \in D \subset R^2$ 上的 **Lipschitz 条件**。

    > Lipschitz 条件保证了导数函数 $f(t, y)$ 在变量 $y$ 上的变化不会过于剧烈，从而确保了初值问题解的存在性和唯一性。

!!! theorem "初值问题的唯一性"
    假设 $D = \{(t, y)\ |\ a \leqslant t \leqslant b, -\infty < y < \infty\}$，且 $f(t, y)$ 在 $D$ 上连续。若 $f$ 满足在变量 $y \in D$ 上的 Lipschitz 条件，那么初值问题
    $$ y'(t) = f(t, y),\ a \leqslant t \leqslant b,\ y(a) = \alpha $$

    有**唯一解** $y(t)$（$a \leqslant t \leqslant b$）。

!!! definition
    若初值问题
    $$ y'(t) = f(t, y),\ a \leqslant t \leqslant b,\ y(a) = \alpha $$

    满足以下条件，我们称之为**适定问题**(well-posed problem)：

    - 存在性：在区间 $[a, b]$ 上存在解 $y(t)$。
    - 唯一性：解 $y(t)$ 在区间 $[a, b]$ 上唯一。
    - $\forall \varepsilon > 0$，存在正常数 $k(\varepsilon)$，使得当 $|\varepsilon_0| < \varepsilon$，并且 $\delta(t)$ 在 $[a, b]$ 上连续且 $|\delta(t)| < \varepsilon$ 时，对于
        $$ z'(t) = f(t, z) + \delta(t),\ a \leqslant t \leqslant b,\ z(a) = \alpha + \varepsilon_0 $$

        存在唯一解 $z(t)$，满足 $|z(t) - y(t)| < k(\varepsilon) \cdot  \varepsilon\ (a \leqslant t \leqslant b)$

        > 上述式子称为**扰动初值问题**(perturbed initial-value problem)

!!! theorem
    假设 $D = \{(t, y)\ |\ a \leqslant t \leqslant b,\ -\infty < y < \infty\}$，且 $f(t, y)$ 在 $D$ 上连续。若 $f$ 满足在变量 $y \in D$ 上的 Lipschitz 条件，那么初值问题
    $$ y'(t) = f(t, y),\ a \leqslant t \leqslant b,\ y(a) = \alpha $$

    是**适定的**(well-posed)。

## Euler's Method

> 欧拉法的目标与上一节相同

考虑函数 $y(t)$ 在点 $t_0$ 处定义：
$$ \begin{aligned}
& y'(t_0) \approx \dfrac{y(t_0 + h) - y(t_0)}{h} \\\\
\Longrightarrow \, &y(t_1) \approx y(t_0) + hy'(t_0) = \alpha + hf(t_0, \alpha)
\end{aligned} $$

其中 $h$ 是步长，$t_1 = t_0 + h$。

**欧拉法**(Euler's method)的核心是用切线近似曲线，它通过**差分方程**(difference equations)来计算近似值：
$$ \begin{cases}
w_0 = \alpha \\\\
w_{i+1} = w_i + hf(t_i, w_i)
\end{cases} \quad (i = 0, \cdots, n - 1) $$

<figure markdown="span">
    ![](./assets/chap-5-1.png){width=80%}
</figure>

> 可以理解为用一个点的导数值代替该点之后一小段距离内的导数值

!!! theorem
    假设 $f$ 在 $D = \{(t, y)\ |\ a \leqslant t \leqslant b, -\infty < y < \infty \}$ 上是连续的，且满足 Lipschitz 条件（对应常数 $L$）；且存在常数 $M,\ \forall\ a \leqslant t \leqslant b$，满足 $|y''(t)| \leqslant M$。

    令 $y(t)$ 为初值问题 $y'(t) = f(t, y), a \leqslant t \leqslant b, y(a) = \alpha$ 的唯一解，且 $w_0, w_1, \cdots, w_n$ 为通过欧拉法（对于某些正整数 $n$）得到的近似值，那么误差界：
    $$ |y_i - w_i| \leqslant \dfrac{hM}{2L}[e^{L(t_i - a)} - 1] \quad (i = 0, 1, \cdots, n) $$

!!! note
    即使我们不知道 $y(t)$ 的值，我们也可以计算出 $y''(t)$：
    $$ \begin{aligned}
    y''(t) &= \dfrac{d}{dt}y'(t) = \dfrac{d}{dt}f(t, y(t)) \\\\
    &= \dfrac{\partial}{\partial t}f(t, y(t)) + \dfrac{\partial}{\partial y}f(t, y(t)) \cdot f(t, y(t))
    \end{aligned} $$

代入舍入误差后，差分方程变为：
$$\begin{cases}
w_0 = \alpha \textcolor{red}{+ \delta_0} \\ w_{i+1} = w_i + hf(t_i, w_i) \textcolor{red}{+ \delta_{i+1}}
\end{cases}
\quad (i = 0, \cdots, n - 1)
$$

!!! theorem
    令 $y(t)$ 为初值问题 $y'(t) = f(t, y), a \leqslant t \leqslant b, y(a) = \alpha$ 的唯一解，且 $w_0, w_1, \cdots, w_n$ 为使用上述差分方程得到的近似值。若 $|\delta_i| < \delta\ (i = 0, \cdots, n)$，那么 $\forall\ i$
    $$ |y_i - w_i| \leqslant \dfrac{1}{L} \left(\dfrac{hM}{2} + \dfrac{\delta}{h}\right)[e^{L(t_i - a)} - 1] + |\delta_0|e^{L(t_i - a)} $$

    其中 $h \geqslant \sqrt{2 \delta / M}$

    > 这个定理说明了舍入误差对欧拉法结果的影响，并提供了一个误差界。

## High Order Taylor Methods

!!! definition
    我们定义差分法 
    $$ \begin{cases}
    w_0 = \alpha \\ 
    w_{i+1} = w_i + h\varphi(t_i, w_i)
    \end{cases} \quad (i = 0, \cdots, n - 1) $$ 

    的**局部截断误差**（local truncation error）为：
    $$ \tau_{i+1}(h) = \dfrac{y_{i+1} - (y_i + h\varphi(t_i, y_i))}{h} = \dfrac{y_{i+1} - y_i}{h} - \varphi(t_i, y_i) \quad (i = 0, \cdots, n - 1)  $$

!!! note
    局部截断误差的定义为 $\dfrac{y_{i+1} - w_{i+1}}{h}$（基于 $w_i = y_i$ 的假设）。

### The local truncation error of Euler’s method

根据以上的定义，我们可以知道欧拉法的局部截断误差为
$$ \begin{aligned}
\tau_{i+1} & = \dfrac{y_{i+1} - w_{i+1}}{h} = \dfrac{[y_i + hy'(t_i) + \frac{h^2}{2}y''(\xi_i)] - [y_i + hf(t_i, y_i)]}{h} \\\\
& = \dfrac{h}{2} y''(\xi_i) = O(h) 
\end{aligned} $$

!!! tip
    上面这一步的推导中使用了泰勒展开：
    $$ y_{i+1} = y(t_{i+1}) = y_i + hy'(t_i) + \frac{h^2}{2}y''(\xi_i) $$

因此欧拉法本质上就是泰勒展开的一阶形式，也就是说，我们可以通过 $n=1$ 的泰勒方法来近似 $y(t)$。

### Higher Order Taylor Methods

我们可以将泰勒展开式推广到更高阶，从而得到更高精度的近似。将 $y_{i+1}$ 在 $t_i$ 处展开到 $n$ 阶：
$$ \begin{aligned}
y_{i+1} &= y_i + hy'(t_i) + \dfrac{h^2}{2!}y''(t_i) + \cdots + \dfrac{h ^ n}{n!}y ^ {(n)}(t_i) + \dfrac{h ^ {n+1}}{(n+1)!}y ^ {(n+1)}(\xi_i) \\\\
&= y_i + hf(t_i, y_i) + \dfrac{h ^ 2}{2} f'(t_i, y_i) + \cdots + \dfrac{h ^ n}{n!}f ^ {(n-1)}(t_i, y_i) + \dfrac{h ^ {(n+1)}}{(n+1)!}f ^ {(n)}(\xi_i, y(\xi_i))
\end{aligned} $$

对于 $n$ 阶的泰勒方法，对应的差分方程为
$$ \begin{cases}
w_0 = \alpha \\\\
w_{i+1} = w_i + hT^{(n)}(t_i, w_i)
\end{cases} \quad (i = 0, \cdots, n - 1) $$

其中 $T^{(n)}(t_i, w_i) = f(t_i, w_i) + \dfrac{h}{2}f'(t_i, w_i) + \cdots + \dfrac{h^{n-1}}{n!} f^{(n-1)}(t_i, w_i)$

若 $y \in C^{n+1}[a, b]$，那么局部截断误差是 $O(h^n)$ 的。

!!! example
    > 注意，这个题目中的 $n$ 表示的是区间划分的份数，而不是泰勒方法的阶数

    <figure markdown="span">
        ![](./assets/chap-5-2.png){width=80%}
    </figure>

### Other Euler’s Methods

- **隐式欧拉法**(implicit Euler's method)
    $$ \begin{aligned}
    &y'(t_0) \approx \dfrac{y(t_0) - y(t_0 - h)}{h} \\\\ \Longrightarrow \ &\textcolor{red}{y(t_1)} \approx y(t_0) + hy'(t_1) + \alpha + hf(t_1, \textcolor{red}{y(t_1)}) 
    \end{aligned} $$

    <figure markdown="span">
        ![](./assets/chap-5-3.png){width=80%}
    </figure> 

    - 差分方程为 $\begin{cases}w_0 = \alpha \\ \textcolor{red}{w_{i+1}} = w_i + hf(t_{i+1}, \textcolor{red}{w_{i+1}})\end{cases}\ (i = 0, \cdots, n - 1)$
        - 这个方法之所以被称为“隐式”，是因为差分方程左右两边都有 $w_{i+1}$，这意味着我们需要解一个关于 $w_{i+1}$ 的隐式方程，而无法像一般的欧拉法（相对地，我们将其称为**显式欧拉法**）那样直接计算得到 $w_{i+1}$
    - 通常以迭代形式求解 $w_{i+1}$，其初始值通过显式法给出
    - 隐式欧拉法的局部截断误差为 
        $$ \tau_{i+1} = \dfrac{y_{i+1} - w_{i+1}}{h} = -\dfrac{h}{2}y''(\xi_i) = O(h) $$
    - 相比显式法，该方法更为**稳定**，因为计算 $w_{i+1}$ 时用到了未来的信息 $t_{i+1}, w_{i+1}$，从而能更准确地预测未来趋势，对解的约束也就越强；而显式法只用了当前的信息 $t_{i}, w_{i}$，其预测结果自然会有更大的偏差

- **梯形法**(trapezoidal method)
    - 差分方程为 $\begin{cases}w_0 = \alpha \\ \textcolor{red}{w_{i+1}} = w_i + \dfrac{h}{2}[f(t_i, w_i) + f(t_{i+1}, \textcolor{red}{w_{i+1}})]\end{cases}\ (i = 0, \cdots, n - 1)$
    - 注：局部截断误差为 $O(h^2)$；但也必须以迭代方式求解隐式方程

- **双步法**(double-step method)
    $$ \begin{aligned}
    & y'(t_0) = \dfrac{1}{2h}[y(t_0 + h) - y(t_0 - h)] - \dfrac{h ^ 2}{6}y ^ {(3)}(\xi_1) \\\\ 
    \Longrightarrow \ & y(t_2) \approx y(t_0) + 2hf(t_1, y(t_1))
    \end{aligned} $$
    - 差分方程为 $\begin{cases}w_0 = \alpha \\ w_{i+1} = w_{i-1} + 2hf(t_i, w_i)\end{cases} (i = 1, \cdots, n - 1)$
    - 这个方法要求知道两个初始点，故得其名；而先前讨论的方法都是**单步法**(single-step methods)
        - 也就是说，双步法除了要知道 $w_0$（即原来的初值）外，还要知道 $w_1$ 的值，因此需要先用单步法得到 $w_1$ 后再用多步法
    - 若假设 $w_{i-1} = y_{i-1}, w_i = y_i$，那么局部截断误差为 $O(h^2)$
    - 由于双步法用到了更多点的信息，因此能得到更精确的近似结果

!!! note "各种欧拉方法的对比"
    |方法|优点|缺点|
    |:-:|:-:|:-:|
    |显式欧拉法|简单|低阶精度|
    |隐式欧拉法|稳定|低阶精度、耗时（计算量大）|
    |梯形法|更精确|耗时|
    |两步法|更精确、显式|要求一个额外的初始点|

## Runge-Kutta Methods

基于泰勒的方法需要计算 $f(t,y)$ 的导数并求其值，在实际应用中这是一个复杂且耗时的过程。而**Runge-Kutta 法**是一种具有高阶局部截断误差的单步方法，无需计算 $f$ 的导数。

思路：在单步法中，某个线段从点 $(t_i, w_i)$ 出发，以某个斜率延伸至下一个点 $(t_{i+1}, w_{i+1})$。我们可以通过**找到更好的斜率**来改善结果。

观察以下修改过的欧拉法（即改进欧拉法，下面会介绍的）：
$$ \begin{cases}
w_{i+1} = w_i + h\Big[ \dfrac{1}{2} K_1 + \dfrac{1}{2} K_2 \Big] \\\\
K_1 = f(t_i, w_i) \\\\
K_2 = f(t_i + h, w_i + hK_1)
\end{cases} $$

考虑两个问题：

- 斜率是否必须是 $K_1, K_2$ 的平均值？
- 步幅是否必须为 $h$？

我们可以将上式推广为更一般的形式（即斜率从平均值变为加权平均值，步幅也可以不同）：
$$ \begin{cases}
w_{i+1} & = w_i + h[\textcolor{red}{\lambda_1} K_1 + \textcolor{red}{\lambda_2} K_2 ] \\\\
K_1 & = f(t_i, w_i) \\\\
K_2 & = f(t_i + \textcolor{red}{p}h, w_i + \textcolor{red}{p}hK_1)
\end{cases} $$

于是现在我们的目标变成了找到合适的 $\lambda_1, \lambda_2, p$，使得该方法的局部阶段误差的阶数为 2。

1. 写出 $K_2$ 在 $(t_i, y_i)$ 上的泰勒展开式：
    $$ \begin{aligned}
    K_2 & = f(t_1 + ph, y_i + phK_1) \\\\
    & = f(t_i, y_i) + phf_t(t_i, y_i) + phK_1f_y(t_i, y_i) + O(h^2) \\\\
    & = y'(t_i) + phy''(t_i) + O(h^2)
    \end{aligned} $$

    ??? tip
        $$ \begin{aligned}
        y''(t) & = \dfrac{d}{dt}f(t, y) \\\\
        & = f_t(t, y) + f_y(t, y) \dfrac{dy}{dt} \\\\
        & = f_t(t, y) + f_y(t, y)f(t, y)
        \end{aligned} $$

2. 将 $K_2$ 代入到第一个式子中，得到
    $$ \begin{aligned}
    w_{i+1} & = y_i + h\{\lambda_1 y'(t_i) + \lambda_2[y'(t_i) + phy''(t_i) + O(h^2)]\} \\\\
    & = y_i + (\lambda_1 + \lambda_2) hy'(t_i) + \lambda_2 ph^2 y''(t_i) + O(h^3)
    \end{aligned} $$

3. 找到 $\lambda_1,\ \lambda_2,\ p$，使得 $\tau_{i+1} = (y_{i+1} - w_{i+1}) / h = O(h^2)$。其中目标值 $y_{i+1}$ 与估计值 $w_{i+1}$ 分别为
    $$ \begin{aligned}
    w_{i+1} & = y_i + (\lambda_1 + \lambda_2) hy'(t_i) + \lambda_2 ph^2 y''(t_i) + O(h^3) \\\\
    y_{i+1} & = y_i + hy'(t_i) + \dfrac{h^2}{2} y''(t_i) + O(h^3) 
    \end{aligned} $$
    
比对上面两个方程，可以得到：$\lambda_1 + \lambda_2 = 1, \lambda_2 p = \dfrac{1}{2}$

这是一个有 3 个未知数，2 个方程的方程组，有无穷多个解。而由这两个方程得到的一系列方法被称为 **2 阶Runge-Kutta 方法**（Runge-Kutta method of order 2）

!!! note "二阶 Runge-Kutta 方法的三个特殊形式"
    - **中点法**(midpoint method)：将从二阶泰勒法中的 $T^{(2)}(t, y)$ 用 $f(t + h / 2, y + (h / 2)f(t, y))$ 替换得到的差分方程法。
        $$ \begin{cases}
        w_0 = \alpha \\\\
        w_{i+1} = w_i + hf(t_i + \dfrac{h}{2}, w_i + \dfrac{h}{2}f(t_i, w_i))
        \end{cases}
        \quad (i = 0, \cdots, N - 1)
        $$

    - **改进欧拉法**(modified Euler method)：
        $$ \begin{cases}
        w_0 = \alpha \\\\
        w_{i+1} = w_i + h(\dfrac{1}{2}K_1 + \dfrac{1}{2} K_2) \\\\
        K_1 = f(t_i, w_i) \\\\
        K_2 = f(t_i + h, w_i + hK_1)
        \end{cases}
        \quad (i = 0, \cdots, N - 1)
        $$

    - **Heun 法**：
        $$ \begin{cases}
        w_0 = \alpha \\\\
        w_{i+1} = w_i + h(\dfrac{1}{4}K_1 + \dfrac{2}{4} K_2) \\\\
        K_1 = f(t_i, w_i) \\\\
        K_2 = f(t_i + \dfrac{2}{3}h, w_i + \dfrac{2}{3}hK_1)
        \end{cases}
        \quad (i = 0, \cdots, N - 1)
        $$

    > 改进欧拉法与梯形法某种程度上有些相似：梯形法使用 $f(t_i, w_i)$ 和 $f(t_{i+1}, w_{i+1})$ 的平均值来估计斜率，代替 $f(t,y)$，而改进欧拉法则是用 $f(t_i, w_i)$ 和 $f(t_i + h, w_i + hK_1)$ 的平均值来估计斜率。区别在于前者是隐式的，后者是显式的。

我们也可以用更高阶的 Runge-Kutta 方法来获得更高的精度：
$$ \begin{cases}
w_{i+1} = y_i + h[\textcolor{red}{\lambda_1} K_1 + \textcolor{red}{\lambda_2} K_2 + \cdots + \textcolor{red}{\lambda_m} K_m] \\\\
K_1 = f(t_i, w_i) \\\\
K_2 = f(t_i + \textcolor{red}{\alpha_2} h, w_i + \textcolor{red}{\beta_{21}} hK_1) \\\\
K_3 = f(t_i + \textcolor{red}{\alpha_3} h, w_i + \textcolor{red}{\beta_{31}} hK_1 + \textcolor{red}{\beta_{32}} hK_2) \\\\
\cdots \\\\
K_m = f(t_i + \textcolor{red}{\alpha_m} h, w_i + \textcolor{red}{\beta_{m1}} hK_1 + \textcolor{red}{\beta_{m2}} hK_2 + \cdots + \textcolor{red}{\beta_{m, m-1}} hK_{m-1})
\end{cases} $$

最为常用的是 **4 阶 Runge-Kutta 方法**：
$$ \begin{cases}
w_{i+1} = w_i + \dfrac{h}{6}(K_1 + 2K_2 + 2K_3 + K_4) \\\\\\\\
K_1 = f(t_i, w_i) \\\\\\\\
K_2 = f(t_i + \dfrac{h}{2}, w_i + \dfrac{h}{2} K_1) \\\\\\\\
K_3 = f(t_i + \dfrac{h}{2}, w_i + \dfrac{h}{2} K_2) \\\\\\\\
K_4 = f(t_i + h, w_i + hK_3)
\end{cases} $$

!!! note
    - 在使用 Runge-Kutta 方法时，主要的计算量在于求解 $f(t, y)$ 的值，因为每一步都需要多次计算 $f$。每步的求值次数和局部阶段误差的阶之间的关系如下表所示：

        <figure markdown="span">
            ![](./assets/chap-5-4.png){width=80%}
        </figure>

    - 因为 Runge-Kutta 方法是基于 Taylor 展开的，所以 y 必须足够平滑，才能获得更高阶方法的更高精度。通常情况下，人们更喜欢使用较小步长的低阶方法，而不是使用较大步长的高阶方法。

## Multistep Methods

思路：多个网格点(mesh points)上使用 $y, y'$ 在的线性组合来得到更好的近似值 $y(t_{i+1})$。

**多步法**（multistep method）的一般形式如下：
$$ \begin{aligned}
w_{i+1} &= \textcolor{red}{a_{m-1}} w_i + \textcolor{red}{a_{m-2}} w_{i-1} + \cdots + \textcolor{red}{a_0} w_{i+1-m} \\\\
&+ h[\textcolor{red}{b_m} f_{i+1} + \textcolor{red}{b_{m-1}} f_i + \cdots + \textcolor{red}{b_0} f_{i+1-m}]
\end{aligned} $$

- 在隐式法中，$b_m \neq 0$
- 在显式法中，$b_m = 0$

在 $[t_i, t_{i+1}]$ 上对 $y'(t) = f(t, y)$ 进行积分，得到
$$ y(t_{i+1}) - y(t_i) = \int_{t_i}^{t_{i+1}} f(t, y(t)) dt $$

这一步的关键是**近似计算积分**。不同的近似方法会得到不同的差分方程。

### Adams-Bashforth explicit m-step technique

使用**牛顿后向差分**公式，在 $(t_i, f_i), (t_{i-1}, f_{i-1}), \cdots, (t_{i+1-m}, f_{i+1-m})$ 上对 $f$ 进行插值，得到插值多项式 $P_{m-1}(t)$。或者令 $t = t_i + sh,\ s \in [0, 1]$，我们有：
$$ \int_{t_i}^{t_{i+1}} f(t, y(t)) dt = h \int_0^1 P_{m-1}(t_i + sh) ds + h \int_0^1 R_{m-1} (t_i + sh) ds $$

其中 $R_{m-1}(t)$ 是插值余项，即局部截断误差。

最后可以得到显式公式:
$$ w_{i+1} = w_i + h\int_0^1 P_{m-1}(t_i + sh)ds $$

!!! definition "多步法的局部截断误差"
    多步法的局部截断误差为
    $$ \tau_{i+1}(h) = \dfrac{y_{i+1} - (a_{m-1}y_i + \cdots + a_0 y_{i+1-m})}{h} - [b_m f_{i+1} + \cdots + b_0 f_{i+1-m}] $$

    其中 $i = m-1, m, \cdots, n - 1$

!!! example
    推导出 Adams-Bashforth 2 步显式法

    **答：** 使用牛顿后向差分公式，在 $(t_i, f_i), (t_{i-1}, f_{i-1})$ 上对 $f$ 插值，得到：
    $$ P_1(t_i + sh) = f_i + s \nabla f_i = f_i + s(f_i - f_{i-1}) $$

    因此得到
    $$ w_{i+1} = w_i + h \int_0^1 [f_i + s(f_i - f_{i-1})] ds = w_i + \dfrac{h}{2} (3f_i - f_{i-1}) $$

    于是局部截断误差为：
    $$ \begin{aligned}
    \tau_{i+1} & = \dfrac{y(t_{i+1}) - w_{i+1}}{h} = \int_0^1 R_1 (t_i + sh) ds \\\\
    & = \int_0^1 \dfrac{d^2 f(\xi_i, t(\xi_i))}{dt^2} \dfrac{1}{2!} sh(s+1)h ds = \dfrac{5}{12} h^2 y'''(\widetilde{\xi_i})
    \end{aligned} $$

!!! note
    一般来说，对于 $\tau = A_mh^my^{(m+1)}(\xi_i)$，其中 $A_m$ 和系数 $f_i, f_{i-1}, f_{i+1-m}$ 能从如下的表格中找到。

    <figure markdown="span">
        ![](./assets/chap-5-5.png){width=80%}
    </figure>

    例如通过这个表格，我们可以知道 Adams-Bashforth 4 步显式法为：
    $$ w_{i+1} = w_i + \dfrac{h}{24} (55f_i - 59f_{i-1} + 37f_{i-2} - 9f_{i-3}) $$

### Adams-Moulton Implicit m-step Technique

Adams-Moulton 法与 Adams-Bashforth 法类似，但它使用**牛顿前向差分**公式：在 $(t_{i+1}, f_{i+1}), (t_i, f_i), \cdots, (t_{i+1-m}, f_{i+1-m})$ 上对 $f$ 进行插值，得到插值多项式 $P_m(t)$。

> 注意这里多了一个点 $(t_{i+1}, f_{i+1})$，因此得到的是隐式方法。

通过类似的方法，我们可以得到截断误差为 $\tau_{i+1} = B_m h^{m+1} y^{(m+2)} (\xi_i)$ 的隐式公式，可以通过查询以下的表格得到系数：

<figure markdown="span">
    ![](./assets/chap-5-6.png){width=80%}
</figure>

例如 Adams-Moulton 3 步隐式法为：
$$ w_{i+1} = w_i + \dfrac{h}{24} (9f_{i+1} + 19f_i - 5f_{i-1} + f_{i-2}) $$

### Adams predictor-corrector system

Adams 预测-校正系统是 Adams-Bashforth 显式 m 步方法和 Adams-Moulton 隐式 m 步方法的结合：

1. 用 **Runge-Kutta 法**计算前 $m$ 个初始值（为 $m$ 步的多步法做准备）
2. 用 **Adams-Bashforth 显式法**进行预测（效率高）
3. 用 **Adams-Moulton 隐式法**进行校正（提高精度，更稳定）

!!! note
    - 对于上述步骤用到的三个公式，它们的局部截断误差必须有**相同的阶数**。
    - 最常用的系统是将 4 阶 Adams-Bashforth 法作为预测器，将 1 次迭代下的 Adams-Moulton 法作为校正器，而起始值通过 4 阶 Runge-Kutta 法获得。

### Derive from Taylor Expansion

回顾多步法的一般形式为：
$$ \begin{aligned}
w_{i+1} &= \textcolor{red}{a_{m-1}} w_i + \textcolor{red}{a_{m-2}} w_{i-1} + \cdots + \textcolor{red}{a_0} w_{i+1-m} \\\\
&+ h[\textcolor{red}{b_m} f_{i+1} + \textcolor{red}{b_{m-1}} f_i + \cdots + \textcolor{red}{b_0} f_{i+1-m}]
\end{aligned} $$

我们的思路是把 $y(t_{i-1}), \cdots, y(t_{i+1-m})$ 以及 $f(t_{i+1}), \cdots, f(t_{i+1-m})$ 在 $t_i$ 处展开成泰勒级数，然后将它们代入上式中，最后通过比对系数来求解 $a_0, a_1, \cdots, a_{m-1}, b_0, b_1, \cdots, b_m$。

!!! example
    <figure markdown="span">
        ![](./assets/chap-5-7.png){width=80%}
    </figure>

上面这个方程组里方程的数量小于未知数的数量，因此有无穷多个解。通过选择不同的解，我们可以得到不同的多步法。

- 令 $a_0 = a_1 = 0$ -> **Adams-Bashforth 显式法**
- 把 $f_{i-1}$ 替换为 $f_{i+1}$，并且令 $a_0 = a_1 = 0$ -> **Adams-Moulton 隐式法**
- 把 $f_{i-3}$ 替换为 $w_{i-3}$，我们可以得到另一族阶数为 4 的方法，包括了显式 **Milne 法**：
    $$ w_{i+1} = w_{i-3} + \dfrac{4h}{3}(2f_i - f_{i-1} + 2f_{i-2}) $$

    其中局部截断误差为 $\tau_{i+1} = \dfrac{14}{45}h^4y^{(5)}(\xi_i),\ \xi_i \in (t_{i-3}, t_{i+1})$

- 令 $a_0 = 0, a_1 = 1$ -> **Simpson 隐式法**
    $$ w_{i+1} = w_{i-1} + \dfrac{h}{3}(f_{i+1} + 4f_i + f_{i-1}) $$

    其中局部截断误差为 $\tau_{i+1} = -\dfrac{h^4}{90} y^{(5)}(\xi_i),\ \xi_i \in (t_{i-1}, t_{i+1})$

## Higher-Order Equations and Systems of Differential Equations

### m-th Order System of 1st-Order IVP

$$ \begin{cases}
u_1'(t) = f_1(t, u_1(t), \cdots, u_m(t)) \\
\cdots \\
u_m'(t) = f_m(t, u_1(t), \cdots, u_m(t))
\end{cases} $$

其中初始条件为 $u_1(a) = \alpha_1,\ u_2(a) = \alpha_2,\ \cdots, u_m(a) = \alpha_m$

令 $\vec{y} = [u_1, u_2, \cdots, u_m]^T$，$\vec{f} = [f_1, f_2, \cdots, f_m]^T$,$\vec{\alpha} = [\alpha_1, \alpha_2, \cdots, \alpha_m]^T$，则上式可以写成向量形式：
$$ \begin{cases}
\vec{y}'(t) = \vec{f}(t, \vec{y}(t)) \\\\
\vec{y}(a) = \vec{\alpha}
\end{cases} $$

### Higher-Order Differential Equation

$$ \begin{cases}
y^{(m)}(t) = f(t, y, y', \cdots, y^{(m-1)}),\ a \leqslant t \leqslant b \\\\
y(a) = \alpha_1, y'(a) = \alpha_2, \cdots, y^{(m-1)}(a) = \alpha_m
\end{cases} $$

思路：将高阶的微分方程归约到一个 1 阶微分方程组。

令 $u_1(t) = y(t), u_2(t) = y'(t), \cdots, u_m(t) = y^{(m-1)}(t)$，得到：
$$ \begin{cases}
u_1' = y' = u_2 \\\\
u_2' = y'' = u_3 \\\\
\vdots \\\\
u_{m-1}' = y^{(m-1)} = u_m \\\\
u_m' = y^{(m)} = f(x, u_1, \cdots, u_m)
\end{cases} $$

初始条件为 $u_1(a) = \alpha_1, u_2(a) = \alpha_2, \cdots, u_m(a) = \alpha_m$。

!!! example
    <figure markdown="span">
        ![](./assets/chap-5-8.png){width=80%}
    </figure>

    !!! tip
        改进欧拉法的公式为：
        $$ \begin{cases}
        w_{i+1} = w_i + h(\dfrac{1}{2}K_1 + \dfrac{1}{2} K_2) \\\\
        K_1 = f(t_i, w_i) \\\\
        K_2 = f(t_i + h, w_i + hK_1)
        \end{cases} $$

## Stability

!!! definition
    - 当局部截断误差为 $\tau_i(h)$ 的单步微分方程法满足下面的条件时，我们认为它和近似得到的微分方程是**一致的**(consistent)：
        $$ \lim\limits_{h \rightarrow 0} \max\limits_{1 \leqslant i \leqslant n} |\tau_i(h)| = 0 $$

        对于多步法，还要求对于 $i = 1, 2, \cdots, m-1$，有 $\lim\limits_{h \rightarrow 0}|w_i - y_i| = 0$

    - 当满足下面的条件时，我们认为一步微分方程法关于近似得到的微分方程是**收敛的**(convergent)：
        $$ \lim\limits_{h \rightarrow 0} \max\limits_{0 \leqslant i \leqslant n} |w_i - y_i| = 0 $$

        对于多步法，同样还要求对于 $i = 1, 2, \cdots, m-1$，有 $\lim\limits_{h \rightarrow 0}|w_i - y_i| = 0$

    - 若在初始条件中的小改变或小扰动只会对近似值产生较小的变化，那么称该方法是**稳定的**（stable）。

!!! example
    <figure markdown="span">
        ![](./assets/chap-5-9.png){width=80%}
    </figure>

!!! definition
    将某个方法用在一个简单的**测试方程**（test equation）上：$y' = \lambda y, y(0) = \alpha$，其中 $\lambda$ 是复数且 $\text{Re}(\lambda) < 0$。
    
    假设舍入误差仅在初始点被引入。如果这个初始误差在特定步幅 $h$ 上被缩小的话，那么该方法关于 $H = \lambda h$ 是**绝对稳定的**（absolutely stable）。所有 $H$ 构成的集合称为**绝对稳定性区域**（the region of absolute stability）。

    当方法 $A$ 的绝对稳定性区域大于方法 $B$ 的时，称 $A$ 比 $B$ **更稳定**。

    <figure markdown="span">
        ![](./assets/chap-5-10.png){width=80%}
    </figure>

!!! example
    === "例一"

        ![](./assets/chap-5-11.png){width=30% align=right}

        考虑显式欧拉法 $w_{i+1} = w_i + hf_i$

        将其应用于测试方程 $y' = \lambda y$，并且因为 $H = \lambda h,\ w_0 = \alpha$，所以有
        $$ w_{i+1} = w_i + h\lambda w_i = (1 + H) w_i = \alpha (1 + H)^{i+1} $$

        考虑到初始点上的误差 $\varepsilon$，则有
        $$ \begin{aligned}
        & \alpha^ * = \alpha + \varepsilon \\\\
        \Rightarrow\ & w_{i+1} ^ * = \alpha ^ * (1 + H)^{i+1} \\\\
        \Rightarrow\ & \varepsilon_{i+1} = w_{i+1} ^ * - w_{i+1} = (1 + H)^{i + 1} \varepsilon
        \end{aligned} $$
            
        因此要想保证误差减小，必须满足 $|1 + H| < 1$，对应的稳定性区域（绿色部分）如右图所示。

    === "例二"

        ![](./assets/chap-5-12.png){width=30% align=right}

        考虑隐式欧拉法 $w_{i+1} = w_i + hf_{i+1}$

        同样将其应用于测试方程 $y' = \lambda y$，得到
        $$ w_{i+1} = \left(\dfrac{1}{1 - H}\right)w_i \Rightarrow \varepsilon_{i+1} = \left(\dfrac{1}{1 - H}\right)^{i+1} \varepsilon $$
            
        因此要想保证误差减小，必须满足 $|1 - H| > 1$，对应的稳定性区域（绿色部分）如右图所示。

        !!! tip
            事实上它是**无条件稳定的**（unconditionally stable），因为只要 $\text{Re}(\lambda) < 0$，那么对于任意的 $h > 0$ 都有 $|1 - H| > 1$（一定处在左半平面内）。

    === "例三"

        ![](./assets/chap-5-13.png){width=30% align=right}

        考虑 2 阶 Runge-Kutta 隐式法：
        $$ \begin{cases}
        w_{i+1} = w_i + hK_1 \\\\
        K_1 = f(t_i + \dfrac{h}{2}, w_i + \dfrac{h}{2}K_1)
        \end{cases} $$

        可以得到
        $$ K_1 = \dfrac{\lambda w_i}{1 - \frac{\lambda h}{2}} $$

        进而得到递推关系为
        $$ w_{i+1} = \left(\dfrac{2+H}{2-H}\right) w_i $$ 

        因此要想保证误差减小，必须满足 $\left|\dfrac{2+H}{2-H}\right| < 1$，实际上这对于任意的 $H$ 满足 $\text{Re}(H) < 0$ 都成立，因此该方法也是无条件稳定的。

        ??? note "补充：1-4 阶的 Runge-Kutta 显式法"
            ![](./assets/chap-5-14.png){width=30% align=right}

            递推方程为：
            $$ \begin{cases}
            w_{i+1} & = w_i + \dfrac{h}{6} (K_1 + 2K_2 + 2K_3 + 2K_4) \\\\
            K_1 & = f(t_i, w_i) \\\\
            K_2 & = f(t_i + \dfrac{h}{2}, w_i + \dfrac{h}{2} K_1) \\\\
            K_3 & = f(t_i + \dfrac{h}{2}, w_i + \dfrac{h}{2} K_2) \\\\
            K_4 & = f(t_i + h, w_i + \dfrac{h}{2} K_3)
            \end{cases} $$

            将其应用于测试方程 $y' = \lambda y$，得到
            $$ \begin{cases}
            K_1 & = \lambda w_i \\\\
            K_2 & = \lambda w_i (1 + \dfrac{H}{2}) \\\\
            K_3 & = \lambda w_i (1 + \dfrac{H}{2} + \dfrac{H^2}{4}) \\\\
            K_4 & = \lambda w_i (1 + H + \dfrac{H^2}{2} + \dfrac{H^3}{4}) \\\\
            w_{i+1} & = w_i \left(1 + H + \dfrac{H^2}{2} + \dfrac{H^3}{6} + \dfrac{H^4}{24}\right)
            \end{cases} $$


!!! example
    <figure markdown="span">
        ![](./assets/chap-5-15.png){width=80%}
    </figure>
