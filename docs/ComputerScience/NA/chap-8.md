---
    comments: true
    statistics: true
---

# Chap 8: Approximation Theory

逼近理论（Approximation Theory）主要研究如何用简单的函数来逼近复杂的函数，以便于计算和分析。形式化地来说，给定 $x_1,\ \cdots,\ x_n$，以及函数 $f(x)$ 在这些点上的值 $y_1 = f(x_1),\ \cdots,\ y_m = f(x_m)$，我们希望找到一个简单的函数 $P(x)$，使得在这些点上 $P(x_i) \approx f(x_i)$，即 $P(x)$ 能够很好地逼近 $f(x)$。

然而，因为有时 $m$ 会特别大，并且 $y_i$ 来自于并不准确的实验数据（即 $y_i \neq f(x_i)$），此时更合理的做法是去寻找能最佳拟合的 $P(x)$，使得对于所有点而言，$P(x_i) - y_i$ 尽可能小。

## Discrete Least Squares Approximation

!!! tip "目标"
    寻找一个多项式 $P_n(x) = a_0 + a_1 x + \cdots + a_n x^n$，用于近似表示一组数据 $\{(x_i, y_i)\ |\ i = 1, 2, \cdots, m\}$，使得最小二乘误差 $E_2 = \sum\limits_{i=1}^m [P_n(x_i) - y_i]^2$ 最小化，其中 $n \ll m$

这里的 $E_2$ 实质上是一个关于 $a_0, a_1, \cdots, a_n$ 的函数
$$ E_2(a_0, a_1, \cdots, a_n) = \sum\limits_{i=1}^m [a_0 + a_1 x_i + \cdots + a_n x_i^n - y_i]^2 $$

$E_2$ 最小化的必要条件是 $\dfrac{\partial E_2}{\partial a_k} = 0,\ k = 0, \cdots, n$，从而我们可以得到

$$ \begin{aligned}
0 &= \dfrac{\partial E_2}{\partial a_k} = 2\sum\limits_{i=1}^m [P_n(x_i) - y_i] \dfrac{\partial P_n(x_i)}{\partial a_k} = 2 \sum\limits_{i=1}^m \Big[\sum\limits_{j=0}^n a_j x_i^j - y_i \Big]x_i^k  \\\\
&= 2\Big\{\sum\limits_{j=0}^n a_j \Big(\sum\limits_{i=1}^m x_i^{j+k}\Big) - \sum\limits_{j=1}^m y_i x_i^k\Big\} 
\end{aligned} $$

整理可以得到
$$ \sum\limits_{j=0}^n a_j \Big(\sum\limits_{i=1}^m x_i^{j+k}\Big) = \sum\limits_{i=1}^m y_i x_i^k,\quad k = 0, 1, \cdots, n $$

将其写为矩阵形式即为

$$
\begin{bmatrix}
\sum\limits_{i=1}^m x_i^0 & \sum\limits_{i=1}^m x_i^1  & \cdots & \sum\limits_{i=1}^m x_i^n \\
\sum\limits_{i=1}^m x_i^1 & \sum\limits_{i=1}^m x_i^2 & \cdots & \sum\limits_{i=1}^m x_i^{n+1} \\
\vdots & \vdots & \ddots & \vdots \\
\sum\limits_{i=1}^m x_i^n & \sum\limits_{i=1}^m x_i^{n+1} & \cdots & \sum\limits_{i=1}^m x_i^{2n} \\
\end{bmatrix}
\begin{bmatrix}
a_0 \\ a_1 \\ \vdots \\ a_n
\end{bmatrix} =
\begin{bmatrix}
\sum\limits_{i=1}^m y_i x_i^0 \\
\sum\limits_{i=1}^m y_i x_i^1 \\
\vdots \\
\sum\limits_{i=1}^m y_i x_i^n \\
\end{bmatrix}
$$

为了公式的简洁起见，我们令 $b_k = \sum\limits_{i=1}^m x_i^k, c_k = \sum\limits_{i=1}^m y_i x_i^k$，那么：

$$
\begin{bmatrix}
b_0 & b_1 & \cdots & b_n \\
b_1 & b_2 & \cdots & b_{n+1} \\
\vdots & \vdots & \ddots & \vdots \\
b_n & b_{n+1} & \cdots & b_{2n} \\
\end{bmatrix}
\begin{bmatrix}
a_0 \\ a_1 \\ \vdots \\ a_n
\end{bmatrix} =
\begin{bmatrix}
c_0 \\ c_1 \\ \vdots \\ c_n
\end{bmatrix}
$$

!!! note
    - $P_n(x)$ 的阶数由使用者指定，且必须不得超过 $m-1$。若 $n=m-1$，那么 $P_n(x)$ 就是拉格朗日插值多项式，其中 $E_2 = 0$
    - 如果考虑每一个点 $x_i$ 的权重 $w_i$，LSA 的公式就变成：
        $$ E_2 = \sum\limits_{i=1}^m w_i [P_N(x_i) - y_i]^2 $$ 
    - 因为 $P_n(x)$ 是多项式函数，因此我们称它是“离散的”。事实上逼近函数 $P(x)$ 未必一定是多项式

对于一些不太适合直接用多项式函数来拟合的离散数据点，我们可以想办法把它转换成线性的形式来拟合，例如下面的例子：

!!! example "非线性拟合的例子"
    <figure markdown="span">
        ![](./assets/chap-8-1.png){width=70%}
    </figure>

    - **方法一**：

        观察函数图像，令 $y \approx P(x) = \dfrac{x}{ax + b}$，我们的目标是寻找 $a, b$，使得 $E_2(a, b) = \sum\limits_{i=1}^m \Big(\dfrac{x_i}{ax_i + b} - y_i\Big)^2$ 最小化。

        上面这个非线性形式的式子显然不太好计算，我们要把它**线性化**(linearization)：令 $Y = \dfrac{1}{y}, X = \dfrac{1}{x}$，就变为了 $Y \approx a + b X$，这就变成了一个线性问题了。

        因此我们可以将 $(x_i, y_i)$ 转换为 $(X_i, Y_i)$，进而可以比较简单地求解出 $a, b$

    - **方法二**：

        同样观察函数图像，但这次令 $y \approx P(x) = ae^{-\frac{b}{x}}$，不难发现 $\ln y \approx \ln a - \dfrac{b}{x}$

        令 $Y = \ln y,\ X = \dfrac{1}{x},\ A = \ln a,\ B = -b$，就变为了 $Y \approx A + B X$，同样是一个线性问题    

        于是我们可以通过将 $(x_i, y_i)$ 转换为 $(X_i, Y_i)$，进而可以比较简单地求解出 $A, B$，最后再通过 $a = e^A,\ b = -B$ 求出 $a, b$，最后得到 $y$ 的近似表达式

!!! example "另一个例子"
    <figure markdown="span">
        ![](./assets/chap-8-2.png){width=80%}
    </figure>

    这道题要求我们在逼近时还要考虑各个数据点的权重，带入带权重的 LSA 公式，可以得到一个关于 $a, b$ 的函数。

    - 显然在所有偏导数都为 0 时函数值最小，直接代入具体数值的计算比较繁琐，我们可以先对偏导数进行化简：
        $$ \begin{aligned}
        \dfrac{\partial E_2}{\partial a} = 0 &\implies \sum w_i x_i y_i = 0 \\\\
        \dfrac{\partial E_2}{\partial b} = 0 &\implies \sum w_i x_i^3 y_i = 0 \\\\
        \end{aligned} $$

        带入上表中的具体值就可以得到关于 $a, b$ 的二元一次方程组，最终解得 $a = \dfrac{8}{7}, b = \dfrac{23}{49}$

## Orthogonal Polynomials and Least Squares Approximation

有时我们已知的是一个较难计算的连续的函数 $f(x)$，而非一组离散的数据点，我们希望找到一个简单的函数 $P(x)$ 来逼近它，使得在区间 $[a, b]$ 上的最小二乘误差尽可能小，具体来说

!!! tip "目标"
    - 离散版本：给定 $x_1, \cdots, x_m;\ y_1, \cdots, y_m$，找到更简单的函数 $P(x) \approx f(x)$，使得 $E = \sum\limits_{i=1}^m |P(x_i) - y_i|^2$ 最小化。
    - 连续版本：给定定义在在 $[a, b]$ 上的函数 $f(x)$，找到更简单的函数 $P(x) \approx f(x)$，使得 $E = \int_a^b [P(x) - f(x)]^2 dx$ 最小化。

### Generalized Polynomial

!!! definition
    对于一组函数 $\{\varphi_0(x),\ \varphi_1(x),\ \cdots,\ \varphi_n(x)\}$，如果对于任意的 $x \in [a, b]$，当 
    $$ a_0 \varphi_0(x) + a_1 \varphi_1(x) + \cdots + a_n \varphi_n(x) = 0 $$ 
    
    时，都有 $a_0 = a_1 \cdots = a_n = 0$，那么称这组函数是**线性独立/线性无关（linearly independent）** 的，否则称它们是**线性相关（linearly dependent）**的。

!!! theorem
    如果 $\varphi_j(x)$ 是一个 $j$ 次多项式（$j = 0, \cdots, n$），那么 $\{\varphi_0(x),\ \varphi_1(x), \cdots,\ \varphi_n(x)\}$ 在任意区间 $[a, b]$ 上都是**线性独立的**(linear independent)。

    > 也就是说，只要一组多项式的次数互不相同，那么它们就一定是线性独立的。

    - 该定理的证明很显然，略。

!!! theorem "多项式构成的线性空间"
    令 $\Pi_n$ 为次数至多为 $n$ 的多项式的集合，如果 $\{\varphi_0(x),\ \varphi_1(x),\ \cdots,\ \varphi_n(x)\}$ 是 $\Pi_n$ 内一组线性独立的多项式，那么 $\Pi_n$ 内的任意多项式均可被唯一写做 $\varphi_0(x),\ \varphi_1(x),\ \cdots,\ \varphi_n(x)$ 的一个线性组合。

    > 显然 $\Pi_n$ 是一个线性空间，而 $\{\varphi_0(x),\ \varphi_1(x),\ \cdots,\ \varphi_n(x)\}$ 就是该线性空间的一组基。

!!! definition "广义多项式"
    对于一般的一组线性独立的函数 $\{\varphi_0(x),\ \varphi_1(x),\ \cdots,\ \varphi_n(x)\}$，它们的**线性组合** $P(x) = \sum\limits_{j=0}^n \alpha_j \varphi_j(x)$ 被称为**广义多项式**(generalized polynomial)。

我们还可以定义一些比较特殊的多项式：

- **三角多项式（trigonometric polynomial）**：
    $$ \\{\cos jx\ \sin jx\\},\quad j = 0, 1, 2, \cdots, n $$

    上面这组函数在区间 $[-\pi, \pi]$ 上是线性独立的，它们的线性组合被称为三角多项式（即傅里叶多项式）：
    $$ P_n(x) = \frac{a_0}{2} + \sum\limits_{j=1}^n [a_j \cos jx + b_j \sin jx] $$

- **指数多项式（exponential polynomial）**：
    $$ \\{ \varphi_j(x) = e^{\lambda_j x} \\},\quad j = 0, 1, 2, \cdots, n $$ 

    其中 $\lambda_j$ 互不相同，上面这组函数在区间 $[a, b]$ 上是线性独立的，它们的线性组合被称为指数多项式：
    $$ P_n(x) = \sum\limits_{j=0}^n a e^{\lambda_j x} $$

### Weighted Least Squares Approximation

!!! definition "权重函数"
    - **离散版本**：
        
        对一组离散点 $(x_i, y_i) (i = 1, \cdots, n)$ 进行近似时，我们为每个一个误差项提供一个权重 $w_i$，它是一个正实数。
    
        此时我们要最小化 
        $$ E = \sum w_i [P(x_i) - y_i]^2 $$
        
        集合 $\{w_i\}$ 被称为**权重**(weight)。设置权重的目标是为这些点赋予不同的“重要程度”，以便对某些数据点进行更精确的拟合。

    - **连续版本**：
    
        一个在区间 $I$ 上的可积分的函数 $w$ 被称为权重函数，如果它满足 $\forall x \in I, w(x) \geqslant 0$，并且 $w(x)$ 不在 $I$ 的任意子区间上都不会消失。
        
        此时我们的目标是最小化 
        $$ E = \int_a^b w(x) [P(x) - f(x)]^2 dx $$

!!! definition "广义最小二乘近似问题"
    - **离散版本**：
    
        给定一组离散点 $(x_i, y_i)$ 和一组对应的权重 $\{w_i\}$（$i = 1, \cdots, m$）。我们要找到一个广义多项式 $P(x)$，使得误差 $E = \sum w_i [P(x_i) - y_i]^2$ 最小化。

    - **连续版本**：
        
        给定定义在区间 $[a, b]$ 上的一个函数 $f(x)$ 和一个权重函数 $w(x)$。我们要找到一个广义多项式 $P(x)$，使得误差 $E = \int_a^b w(x) [P(x) - f(x)]^2 dx$ 最小化。

### Inner Product and Norm

我们可以为函数空间定义一个**内积**（inner product），使用它来衡量两个函数之间的“相似度”。
$$ \langle f, g \rangle = 
\begin{cases}
\sum\limits_{i=1}^m w_i f(x_i) g(x_i) & \text{discrete version} \\\\ 
\int_a^b w(x) f(x) g(x) dx & \text{continuous version} 
\end{cases} $$

可以证明它满足内积的三个性质：

- 线性性：$\langle af + bg, h \rangle = a\langle f, h \rangle + b\langle g, h \rangle$
- 共轭对称性：$\langle f, g \rangle = \overline{\langle g, f \rangle} = \langle g, f \rangle$
- 正定性：$\langle f, f \rangle \geqslant 0$，且当且仅当 $f = 0$ 时取等号

当两个函数 $f, g$ 满足 $\langle f, g \rangle = 0$ 时，我们称它们是**正交的**（orthogonal）。

上面的内积也定义了一个**范数**（norm）：$\| f \| = \sqrt{\langle f, f \rangle}$，它也满足范数的几个性质

- 正定性：$\| f \| \geqslant 0$，且当且仅当 $f = 0$ 时取等号
- 齐次性：$\| af \| = |a| \| f \|$
- 三角不等式：$\| f + g \| \leqslant \| f \| + \| g \|$

于是我们可以使用 $\| f - g \|$ 来衡量函数 $f$ 和 $g$ 之间的差异。

因此一般的最小二乘近似问题可以被转换为：

!!! note ""
    寻找一个广义多项式 $P(x)$，使得误差 $E = \langle P - y, P - y \rangle = \| P - y \|^2$ 最小化。

### Orthogonal Polynomials Approximation

设广义多项式为 $P(x) = a_0 \varphi_0(x) + a_1 \varphi_1(x) + \cdots + a_n \varphi_n(x)$，类似于离散情况，当误差的偏导数均为 0 时取到最小值，根据 $\dfrac{\partial E}{\partial a_k} = 0$ 可以推导得到（推导过程省略）：
$$ \sum\limits_{j=0}^n \langle \varphi_k, \varphi_j \rangle a_j = \langle \varphi_k, f \rangle, \quad k = 0, \cdots, n $$

记 $b_{ij} = \langle \varphi_i, \varphi_j \rangle,\ c_k = \langle \varphi_k, f \rangle$，那么上面的式子可以写成矩阵形式：

$$ 
\begin{bmatrix}
b_{00} & b_{01} & \cdots & b_{0n} \\
b_{10} & b_{11} & \cdots & b_{1n} \\
\vdots & \vdots & \ddots & \vdots \\
b_{n0} & b_{n1} & \cdots & b_{nn} \\
\end{bmatrix}
\begin{bmatrix}
a_0 \\ a_1 \\ \vdots \\ a_n
\end{bmatrix} =
\begin{bmatrix}
c_0 \\ c_1 \\ \vdots \\ c_n
\end{bmatrix}
$$

!!! example "使用普通多项式作为基函数的例子"
    <figure markdown="span">
        ![](./assets/chap-8-3.png){width=85%}
    </figure>

接下来我们再考虑另一个例子：

!!! example
    当我们逼近 $f(x) \in C[0, 1]$ 时如果使用基函数 $\varphi_j(x) = x^j$ 和权重函数 $w(x) \equiv 1$，那么两个基函数之间的内积为
    $$ \langle \varphi_i, \varphi_j \rangle = \int_0^1 x^{i+j} dx = \dfrac{1}{i + j + 1} $$

    此时，矩阵 $B = [b_{ij}]$ 就是著名的**希尔伯特矩阵**(Hilbert matrix)。

    ??? info "希尔伯特矩阵"
        希尔伯特矩阵是一个非常病态的矩阵，这意味着当矩阵的阶数较高时，它的条件数会变得非常大，从而导致数值计算中的不稳定性和误差放大。

        希尔伯特矩阵的元素定义为 $H_{ij} = \dfrac{1}{i + j - 1}$，其中 $i, j = 1, 2, \cdots, n$。

        $$ \begin{bmatrix}
        1 & \dfrac{1}{2} & \dfrac{1}{3} & \cdots & \dfrac{1}{n+1} \\\\
        \dfrac{1}{2} & \dfrac{1}{3} & \dfrac{1}{4} & \cdots & \dfrac{1}{n+2} \\\\
        \vdots & \vdots & \vdots & \ddots & \vdots \\\\
        \dfrac{1}{n+1} & \dfrac{1}{n+2} & \dfrac{1}{n+3} & \cdots & \dfrac{1}{2n+1} \\\\
        \end{bmatrix} $$

    希尔伯特矩阵的条件数随着矩阵阶数的增加而迅速增大，例如：

    - 当 $n = 2$ 时，条件数约为 $19.3$；
    - 当 $n = 3$ 时，条件数约为 $524$；
    - 当 $n = 4$ 时，条件数约为 $1.5 \times 10^4$；
    - 当 $n = 5$ 时，条件数约为 $4.8 \times 10^5$；
    - 当 $n = 10$ 时，条件数约为 $1.6 \times 10^{13}$；

上面这个例子表明，当我们使用普通多项式作为基函数时，可能会遇到数值不稳定的问题。为了解决这个问题，我们可以使用**正交多项式**作为基函数。

具体来说，我们希望能找到一组较为一般的线性独立的函数 $\{\varphi_0(x), \varphi_1(x), \cdots, \varphi_n(x) \}$，使得任何函数对 $\big(\varphi_i(x), \varphi_j(x)\big)$ 都是**正交的**(orthogonal)，那么此时范数矩阵将会是个**对角矩阵**。此时我们有 $a_k = \dfrac{(\varphi_k, f)}{(\varphi_k, \varphi_k)}$

!!! theorem
    按照如下方式构造出的一组在 $[a, b]$ 的多项式函数 $\{\varphi_0(x), \varphi_1(x), \dots, \varphi_n(x)\}$ 关于权重函数 $w$ 是正交的：
    $$ \begin{aligned}
    & \varphi_0 (x) \equiv 1,\quad \varphi_1(x) = x - B_1 \\\\
    & \varphi_k(x) = (x - B_k)\varphi_{k-1}(x) - C_k \varphi_{k-2}(x)
    \end{aligned} $$

    其中 $k = 2, 3, \cdots, n$，且 $B_k = \dfrac{(x \varphi_{k-1}, \varphi_{k-1})}{(\varphi_{k-1}, \varphi_{k-1})},\ C_k = \dfrac{(x \varphi_{k-1}, \varphi_{k-2})}{( \varphi_{k-2}, \varphi_{k-2})}$

!!! tip
    - 这样构造出来的正交多项式都是**首一多项式**(monic polynomial)（即最高次数项系数为1的多项式）。
    - 上述定理的构造过程实际上是在多项式空间中应用 **Gram-Schmidt 正交化过程**(Gram-Schmidt orthogonalization process) 的结果，因此这些多项式自然是正交的。

!!! example "构造正交多项式作为基的例子"
    <figure markdown="span">
        ![](./assets/chap-8-4.png){width=85%}
    </figure>

!!! note "正交多项式逼近的伪代码"
    <figure markdown="span">
        ![](./assets/chap-8-5.png){width=85%}
    </figure>

    其中误差的计算推导如下：

    <figure markdown="span">
        ![](./assets/chap-8-6.png){width=85%}
    </figure>

## Chebyshev Polynomials and Economization of Power Series

### Targeting the Minimax Approximation

和之前一样，我们的目标仍然是寻找一个广义多项式 $P(x)$，使得 $E = (P - y, P - y) = \| P - y \|^2$ 最小化。

假如我们采用的是无限范数 $\| f \|_\infty = \max\limits_{x \in [a, b]} |f(x)|$，那么 $\| P-f \|_\infty$ 的最小化问题实际上就是一个**最小化最大问题**(minimax problem)。

!!! note "target 1.0"
    寻找一个 $n$ 阶多项式 $P_n(x)$，使得 $\| P_n - f \|_{\infty}$ 最小化。

!!! definition "偏差点"
    如果 $P(x_0) - f(x_0) = \pm \|P - f\|_{\infty}$，那么此时 $x_0$ 被称为 $(\pm)$ **偏差点**(deviation point)（也就是正好在误差边界上的点）。

直接无中生有构造出合适的逼近多项式并不容易，但我们能够知道合适的逼近多项式应该有哪些特征：

- 如果 $f \in C[a, b]$ 且 $f$ **不是**一个 $n$ 阶多项式，那么存在一个唯一的多项式 $P_n(x)$，使得 $\|P_n - f\|_{\infty}$ 最小化
- $P_n(x)$ 一定存在，且必须同时有正负偏差点
- **切比雪夫定理**(Chebyshev Theorem)：

    $P_n(x)$ 最小化  $\|P_n - f\|_{\infty}\ \iff P_n(x)$ 至少有 $n+2$ 个关于 $f$ 的正负偏差点。也就是说，存在一组点 $a \leqslant t_1 < \cdots < t_{n+2} \leqslant b$ 使得 
    $$ P_n(t_k) - f(t_k) = \pm(-1)^k \|P_n - f\|_{\infty} $$
    
    集合 $\{t_k\}$ 被称为**切比雪夫交替序列**(Chebyshev alternating sequence)。

    > $P_n(x) - f(x)$ 至少有 $n+1$ 个根

    <figure markdown="span">
        ![](./assets/chap-8-7.png){width=60%}
    </figure>

现在我们知道了该如何去判断一个多项式是否是“足够好的”逼近多项式了。从上图中我们可以知道，$P_n(x)$ 是 $f(x)$ 的一个插值多项式，那么我们现在需要决定在哪些离散点上进行插值，以便得到一个好的逼近多项式。

具体来说：

!!! note "target 2.0"
    我们需要确定插值点 $\{x_0, \cdots, x_n\}$ 使得 $P_n(x)$ 能够最小化余项 
    $$ |P_n(x) - f(x)| = |R_n(x)| = \left|\dfrac{f^{(n+1)}(\xi)}{(n+1)!} \prod\limits_{i=0}^n (x - x_i)\right| $$

最右侧的乘积项与 $f(x)$ 无关，而它是我们估计并减小误差上界的关键，于是问题就变成了：

!!! note "target 2.1"
    找到 $\{x_1, \cdots, x_n\}$ 使得 $\|w_n\|_{\infty}$ 在 $[-1, 1]$ 最小化，其中 $w_n(x) = \prod\limits_{i=1}^n (x - x_i)$

    > 这里的区间 $[-1, 1]$ 只是为了方便起见，实际上我们可以通过变量替换将任意区间 $[a, b]$ 转换为 $[-1, 1]$

而此时我们还可以注意到：$w_n(x) = x^n - P_{n-1}(x)$，其中 $P_{n-1}(x)$ 是一个 $n-1$ 次多项式。所以现在的目标又变成了：

!!! note "target 3.0"
    找到一个 $n-1$ 次多项式 $P_{n-1}(x)$，使得 $\|x^n - P_{n-1}(x)\|_{\infty}$ 在 $[-1, 1]$ 上最小。

根据切比雪夫定理，我们知道 $P_{n-1}(x)$ 有 $n+1$ 个关于 $x^n$ 的偏差点，也就是说 $w_n(x)$ 会在这 $n+1$ 个点上交替取得最大值和最小值。

### Chebyshev Polynomials

$w_n(x)$ 会交替取得最大值和最小值，在形式上我们可以想到三角函数：$\cos(n\theta)$ 在 $[0, \pi]$ 上正好有 $n+1$ 个交替的极值点 $\theta_k = \dfrac{k}{n}\pi$。虽然 $\cos(n\theta)$ 不是多项式，但我们可以把它展开为三角多项式的形式，即
$$ \cos(n\theta) = \sum_{k=0}^n a_k \cos^k \theta $$

令 $x = \cos (\theta)$，那么 $x \in [-1, 1]$。我们称 $T_n(x) = \cos (n\theta) = \cos (n \cdot \text{arc} \cos x)$ 为**切比雪夫多项式**（Chebyshev polonomial）。

!!! property "切比雪夫多项式的性质"
    - $T_n(x)$ 在 $t_k = \cos \Big(\dfrac{k}{n} \pi\Big) (k = 0, 1, \dots, n)$ 处，极值为 $(-1)^k$，也就是说 $T_n(t_k) = (-1)^k \|T_n(x)\|_{\infty} = (-1)^k$

        > 因为 $\|T_n(x)\|_{\infty} = 1$

    - $T_n(x)$ 有 $n$ 个根 $x_k = \cos \Big(\dfrac{2k - 1}{2n} \pi \Big)(k = 1, \dots, n)$
    - $T_n(x)$ 有如下的递推关系式：  
        $$ T_0(x) = 1,\ T_1(x) = x,\ T_{n+1}(x) = 2x T_n(x) - T_{n-1}(x) $$
        - $T_n(x)$ 是一个最高阶系数为 $2^{n-1}$ 的 $n$ 阶多项式
    - $\{T_0(x), T_1(x), \dots\}$ 在 $[-1, 1]$ 上关于权重函数 $w(x) = \dfrac{1}{\sqrt{1 - x^2}}$ 上正交，也就是说 
        $$ \langle T_n, T_m \rangle = \int_{-1}^1 \dfrac{T_n(x) T_m(x)}{\sqrt{1-x^2}} dx = 
        \begin{cases}
        0 & n \ne m \\\\
        \pi & n = m = 0 \\\\ 
        \pi / 2 & n = m \ne 0
        \end{cases} $$

现在我们已经可以把目标多项式 $w_n(x)$ 改写为含有 $T_n(x)$ 的形式：
$$ w_n(x) = \dfrac{1}{2^{n-1}} T_n(x) $$

我们现在把这一条信息逐一代入回我们之前的目标：

!!! note "targets"
    - **target 3.0**：找到多项式 $P_{n-1}(x)$，使得 $\|x^n - P_{n-1}(x)\|_{\infty}$ 在 $[-1, 1]$ 上最小。
        - 此时 $w_n(x) = x^n - P_{n-1}(x) = \dfrac{T_n(x)}{2^{n-1}}$
    - **target 2.1**：找到 $\{x_1,\ \cdots,\ x_n\}$ 使得 $\|w_n\|_{\infty}$ 在 $[-1, 1]$ 上最小化，其中 $w_n(x) = \prod\limits_{i=1}^n (x - x_i)$
        - 此时有
            $$ \min\limits_{w_n \in \widetilde{\Pi} _ n} \|w _ n\| _ {\infty} = \left\|\dfrac{1}{2^{n-1}} T _ n(x)\right\| _ {\infty} = \dfrac{1}{2^{n-1}} $$
            
            其中 $\widetilde{\Pi}_n$ 是 $n$ 阶的**首一多项式**，$\{x_1, \dots, x_n\}$ 是 $T_n(x)$ 的 $n$ 个根

    - **target 2.0**：确定插值点 $\{x_0, \cdots, x_n\}$ 使得 $P_n(x)$ 能够最小化余项 
        $$ |P_n(x) - f(x)| = |R_n(x)| = \left|\dfrac{f^{(n+1)}(\xi)}{(n+1)!} \prod\limits_{i=0}^n (x - x_i)\right| $$

        - 取 $T_{n+1}(x)$ 上的 $n+1$ 个根作为插值点 $\{x_0, \cdots, x_n\}$，此时关于 $f(x)$ 的插值多项式 $P_n(x)$ 可以使绝对误差的上界取到最小值 $\dfrac{M}{2^n (n+1)!}$
    
    !!! tip
        我们这里选取的 $n$ 个插值点位于区间 $[-1, 1]$ 上，但可以通过线性变换 $x = \dfrac{b-a}{2} t + \dfrac{a+b}{2}$ 将其映射到任意区间 $[a, b]$ 上。

!!! example
    <figure markdown="span">
        ![](./assets/chap-8-8.png){width=85%}
    </figure>

### Economization of Power Series

给定 $P_n(x) \approx f(x)$，**幂级数缩减**（economization）指的是在确保精度损失最小的情况下，降低多项式的次数。

考虑使用一个 $n-1$ 阶多项式 $P_{n-1}(x)$ 来逼近一个 $n$ 阶多项式 
$$ P_n(x) = a_n x^n + a_{n-1} x^{n-1} + \cdots + a_1 x + a_0 $$

我们可以通过减去一个 $n$ 阶多项式 $Q_n(x)$（其中 $Q_n(x)$ 的 $x^n$ 项的系数为 $a_n$）来逼近 $P_n(x)$，即 $P_{n-1}(x) = P_n(x) - Q_n(x)$。那么就有 
$$ \max\limits_{[-1, 1]} |f(x) - P_{n-1}(x)| \le \max\limits_{[-1, 1]} |f(x) - P_n(x)| + \max\limits_{[-1, 1]} |Q_n(x)| $$

在这里，$Q_n(x)$ 能够反映精度的损失。

为了最小化精度损失，$Q_n(x)$ 必须为 $a_n \times \dfrac{T_n(x)}{2^{n-1}}$

!!! note

    - 对于一般的区间 $[a, b]$，变量 $x \in [a,b]$ 需要通过线性变换得到。也就是说，令 
        $$ x = \dfrac{b-a}{2} t + \dfrac{a+b}{2} $$
        
        然后寻找在 $[-1, 1]$ 上寻找函数 $f(t)$ 的逼近多项式 $P_n(t)$，最终得到原始区间 $[a,b]$ 上的逼近多项式 $P_n(x)$。

    - 另一种方法是把每一项 $x^k$ 改写为 $T_0(x), \dots, T_k(x)$ 的线性组合的形式。
    
        比如，$x = T_1(x)$，且 $x^3 = [T_3(x) + 3T_1(x)] / 4$。然后只要从原始多项式中带入并消去切比雪夫函数就行了。

!!! example
    <figure markdown="span">
        ![](./assets/chap-8-9.png){width=85%}
    </figure>
