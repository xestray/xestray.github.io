---
    comments: true
    statistics: true
---

# Chap 1: Mathematical Preliminaries

## Roundoff Errors and Computer Arithmetic

- **截断误差（truncation error）**：使用了有限项之和来近似无穷级数之和等情况所引入的误差。
- **舍入误差（roundoff error）**：在进行实数计算时产生的误差。它的产生是因为机器中进行的算术运算只能使用有限位数的数字。

!!! example 
    估计： $\int_0^1 e^{-x^2} dx$

    这个式子的真实值约为 0.747

    使用泰勒展开进行分析：

    $$ \begin{aligned}
    \int_0^1 e^{-x^2} dx &= \int_0^1 \left( 1 - x^2 + \frac{x^4}{2!} - \frac{x^6}{3!} + \frac{x^8}{4!} - \cdots \right) dx \\
    &= 1 - \frac{1}{3} + \frac{1}{2!} \times \frac{1}{5} - \frac{1}{3!} \times \frac{1}{7} + \frac{1}{4!} \times \frac{1}{9} - \cdots
    \end{aligned} $$

    我们把前四项记为 $S_4$，之后的项的和记为 $R_4$（remainder），则有（假设计算机只能表示三位有效数字）：

    $$ \int_0^1 e^{-x^2} dx \approx S_4 = 1 - \frac{1}{3} + \frac{1}{10} - \frac{1}{42} \approx 1 - 0.333 + 0.1 - 0.024 \approx 0.743 $$

    - **舍入误差**：泰勒展开后，$\frac{1}{3}$ 和 $\frac{1}{42}$ 这两项在计算机中被分别舍入了，导致的舍入误差 $< 0.0005 \times 2 = 0.001$。
    - **截断误差**：我们忽略 $R_4$，即忽略了 $\frac{1}{4!} \times \frac{1}{9} - \frac{1}{5!} \times \frac{1}{11} + \cdots$ 这些项。由于这是一个交错级数，且各项绝对值单调递减，因此截断误差 $< \left| \frac{1}{4!} \times \frac{1}{9} \right| < 0.005$。
    - 总误差 $< 0.001 + 0.005 = 0.006$。

假设在能表示 $k$ 位有效数字的机器中，我们用如下的方法表示实数：

$$ \pm \ 0.d_1 d_2 d_3 \cdots d_k \times 10^n $$

其中 $1 \leqslant d_1 \leqslant 9$，$0 \leqslant d_i \leqslant 9 \ (i = 2, 3, \cdots, k)$

那么对于任意一个实数 $y = \pm \ 0.d_1 d_2 d_3 \cdots d_k d_{k+1} d_{k+2} \cdots \times 10^n$，它在机器中有两种表示方法：

$$ fl(y) = \begin{cases}
0.d_1 d_2 d_3 \cdots d_k \times 10^n, & \text{向下取整（chopping）} \\
chop(y + 5 \times 10^{n-k-1}) = 0.\delta_1 \delta_2 \delta_3 \cdots \delta_k \times 10^n, & \text{四舍五入（rounding）}
\end{cases} $$

!!! info "绝对误差与相对误差"
    假设一个数的精确值为 $p$，它的近似值为 $p^*$，那么：

    - **绝对误差**：$|p - p^*|$
    - **相对误差**：$\dfrac{|p - p^*|}{|p|}$

我们称 $p^*$ 近似 $p$ 到 $t$ 位有效数字（或有效位数），如果 $t$ 是满足下列条件的最大非负整数：
$$ \frac{|p - p^*|}{|p|} < 5 \times 10^{-t} $$

- chopping：
    $$ \begin{aligned}
    \left| \frac{y - fl(y)}{y} \right| &= \left| \frac{0.d_1 d_2 d_3 \cdots d_k d_{k+1} \cdots \times 10^n - 0.d_1 d_2 d_3 \cdots d_k \times 10^n}{0.d_1 d_2 d_3 \cdots d_k d_{k+1} \cdots \times 10^n} \right| \\\\
    &= \left| \frac{0.d_{k+1} d_{k+2}}{0.d_1 d_2 \cdots} \right| \times 10^{-k} \leqslant \frac{1}{0.1} \times 10^{-k} = 10^{-k+1}
    \end{aligned} $$

- rounding：
    $$ \left| \frac{y - fl(y)}{y} \right| \leqslant \frac{0.5}{0.1} \times 10^{-k} = 0.5 \times 10^{-k+1} $$

!!! tip
    舍入误差可能会在计算过程中被放大，甚至会导致结果完全错误。

    - 例如 $a_1 = 0.12345, a_2 = 0.12346$，它们都是五位有效数字，但是计算 $a_2 - a_1 = 0.00001$，结果只有一位有效数字，在有效数位很少的情况下，可能会导致结果完全错误。
    - 当乘以一个很大的数或除以一个很小的数时，舍入误差也会被放大。

!!! example
    在可保留三位有效数字的机器中计算 $f(x) = x^3 - 6.1x^2 + 3.2x + 1.5$，其中 $x = 4.71$。

    这个式子的精确值为 $f(4.71) = -14.263899$，
    
    假如我们直接逐项计算这个式子：
    
    - 使用 chopping：$-13.5$（相对误差 5%）
    - 使用 rounding：$-13.4$（相对误差 6%）。

    我们可以使用秦九韶算法（也称 Horner's method）来计算：

    $$ f(x) = ((x - 6.1)x + 3.2)x + 1.5 $$

    这样一来就大大减小了舍入误差的影响：
    
    - 使用 chopping：$-14.2$（相对误差 0.45%）
    - 使用 rounding：$-14.3$（相对误差 0.25%）。

## Algorithms and Convergence

!!! definition "稳定性"
    如初始数据的微小变化仅导致算法最终结果产生相应的微小变化，则称该算法是稳定的（stable）的，否则称为不稳定的（unstable）。

    若算法仅在某些特定的初始数据下是稳定的，则称该算法是条件稳定的（conditionally stable）的。

!!! definition "误差增长"
    设 $E_0 > 0$ 表示初始误差，$E_n > 0$ 表示经过 $n$ 次迭代后的误差。

    - 若 $E_n \approx C \cdot n \cdot E_0$，则称误差是线性增长的。
    - 若 $E_n \approx C^n \cdot E_0$，则称误差是指数增长的。

- 误差的线性增长一般是不可避免的，当 $C$ 和 $E_0$ 较小时，结果一般是可接受的
- 指数增长的误差一般是不可接受的，因为即使 $n$ 较小，$C^n$ 也会快速增大，导致最终结果的不准确

!!! example
    计算 
    $$ I_n = \dfrac{1}{e} \int_0^1 x^n e^x dx $$
    
    其中 $n = 0, 1, 2, \cdots$。

    === "方法一（不稳定）"

        使用分部积分法，有递推关系：
        $$ I_n = 1 - n I_{n-1} $$
        其中 $I_0 = \dfrac{1}{e} \int_0^1 e^x dx = 1 - \dfrac{1}{e} \approx 0.6321205588 = I_0^*$
        $$ |E_0| = |I_0 - I_0^*| < 0.5 \times 10^{-8} $$
        但是我们可以注意到

        $$ \begin{aligned}
        |E_n| &= |I_n - I_n^*| = |(1 - n I_{n-1}) - (1 - n I_{n-1}^*)|  \\
        &= n|E_{n-1}| = n(n-1)|E_{n-2}| = \cdots = n! |E_0|
        \end{aligned} $$

        误差的增长速率甚至还要比指数级更快，这显然是不稳定的

    === "方法二（稳定）"

        同样使用分部积分法得到 $I_n = 1 - n I_{n-1}$，但是此时将它变换为 $I_{n-1} = \dfrac{1}{n} (1 - I_n)$。

        由于
        $$ \dfrac{1}{e} \int_0^1 x^n e^0 dx < I_n < \dfrac{1}{e} \int_0^1 x^n e^1 dx $$
        可得 
        $$ \dfrac{1}{e(n+1)} < I_n < \dfrac{1}{n+1} $$
        我们使用 $I_n^* = \dfrac{1}{2} \left[ \dfrac{1}{e(n+1)} + \dfrac{1}{n+1} \right] $ 作为 $I_n$ 的近似值.$n$ 足够大时，$|E_n|$ 趋向于 0，因此这个近似值是比较好的。

        因此我们可以从一个较大的 $n$ 开始，逐步计算 $I_{n-1}, I_{n-2}, \cdots, I_0$，这样误差不会被放大，算法是稳定的：
        $$ |E_{n-1}| = |\frac{1}{n} (1-I_n) - \frac{1}{n} (1-I_n^*)| = \frac{1}{n} |E_n| $$
        $$ \Rightarrow |E_{n-2}| = \frac{1}{n-1} |E_{n-1}| = \frac{1}{n(n-1)} |E_n| $$
        从而 $ |E_k| = \dfrac{1}{n(n-1) \cdots (k+1)} |E_n| $

        误差增长速率显然比线性增长还要慢，算法是稳定的
