---
    comments: true
    statistics: true
---

# Chap 9: Approximating Eigenvalues

## The Power Method

**幂法**是一种用来计算矩阵主特征值以及对应的特征向量的方法。它的基本思想是通过不断地将一个初始向量与矩阵相乘，从而使得该向量逐渐趋近于主特征向量。

!!! note "主特征值"
    **主特征值（dominant eigenvalue）**是指矩阵中绝对值最大的特征值，这个特征值的绝对值就是矩阵的**谱半径（spectral radius）**（$\rho(A) = \max{|\lambda_i|}$）。对应的特征向量称为主特征向量。

### The Original Method

假设 $A$ 是一个 $n \times n$ 的矩阵，且其特征值按绝对值大小排序为 $|\lambda_1| > |\lambda_2| \geqslant |\lambda_3| \geqslant ... \geqslant |\lambda_n| \geqslant 0$。这些特征值对应的 $n$ 个线性无关的特征向量 $\vec{v_1}, \vec{v_2}, ..., \vec{v_n}$，它们构成了 $R^n$ 的一组基。

因此任意一个非零向量 $\vec{x}^{(0)}$ 都可以表示为这些特征向量的线性组合：
$$ \vec{x}^{(0)} = \sum_{j=1}^{n} \beta_j \vec{v_j} $$

思路：我们从任意 $\vec{x}^{(0)} \neq 0$ 以及 $(\vec{x}^{(0)}, \vec{v_1}) \neq 0$ 开始，不断地左乘 $A$，得到一系列向量：
$$ \begin{aligned}
\vec{x}^{(0)} &= \sum_{j=1}^{n} \beta_j \vec{v_j},\ \beta_1 \neq 0 \\\\
\vec{x}^{(1)} &= A \vec{x}^{(0)} = \sum_{j=1}^{n} \beta_j A \vec{v_j} = \sum_{j=1}^{n} \beta_j \lambda_j \vec{v_j} \\\\
\vec{x}^{(2)} &= A \vec{x}^{(1)} = \sum_{j=1}^{n} \beta_j A^2 \vec{v_j} = \sum_{j=1}^{n} \beta_j \lambda_j^2 \vec{v_j} \\\\
&\cdots \\\\
\vec{x}^{(k)} &= A \vec{x}^{(k-1)} = \sum_{j=1}^{n} \beta_j A^k \vec{v_j} = \sum_{j=1}^{n} \beta_j \lambda_j^k \vec{v_j} \\\\
\end{aligned} $$

我们可以对 $\vec{x}^{(k)}$ 进行变换，得到
$$ \vec{x}^{(k)} = \sum_{j=1}^{n} \beta_j \lambda_j^k \vec{v_j} = \lambda_1^k \sum_{j=1}^{n} \beta_j \left( \frac{\lambda_j}{\lambda_1} \right)^k \vec{v_j} $$

由于 $\left|\dfrac{\lambda_j}{\lambda_1}\right| < 1$ （$j = 2, 3, ..., n$），当 $k \to \infty$ 时，$\left( \dfrac{\lambda_j}{\lambda_1} \right)^k \to 0$，因此当 $k$ 足够大时，$j \geqslant 1$ 的求和项可以被忽略
$$ \vec{x}^{(k)} \approx \lambda_1^k \beta_1 \vec{v_1}, \quad
\vec{x}^{(k-1)} \approx \lambda_1^{k-1} \beta_1 \vec{v_1} $$

于是我们就可以通过它们的比值求出主特征值 $\lambda_1$：
$$ \frac{\vec{x} ^ {(k)}}{\vec{x} ^ {(k-1)}} \approx \lambda_1 $$

同时我们还可以注意到：
$$ A \vec{x}^{(k)} \approx \lambda_1^k \beta_1 A \vec{v_1} = \lambda_1 \cdot \lambda_1^k \beta_1 \vec{v_1} \approx \lambda_1 \vec{x}^{(k)} $$

也就是说，不断迭代后 $\vec{x}^{(k)}$ 会逐渐趋近于主特征向量 $\vec{v_1}$。

因此，我们可以通过不断迭代 $\vec{x}^{(k)} = A \vec{x}^{(k-1)}$ 来逼近主特征值 $\lambda_1$ 以及对应的特征向量 $\vec{v_1}$。

### Normalization

!!! tip
    通过上面的计算，我们可以发现

    $$ \lim_{k \to \infty} A^k \vec{x}^{(0)} = \lim_{k \to \infty} \lambda_1^k \beta_1 \vec{v_1} $$

    - 若 $|\lambda_1| < 1$，则 $\lim\limits_{k \to \infty} A^k \vec{x}^{(0)} = 0$，即 $\vec{x}^{(k)}$ 收敛到 0 向量；
    - 若 $|\lambda_1| > 1$，则 $\lim\limits_{k \to \infty} A^k \vec{x}^{(0)} = \infty$，即 $\vec{x}^{(k)}$ 发散。

因此为了避免在实际计算过程中出现绝对值过大或过小的数参加运算，导致出现不稳定的结果，我们通常会对每一步迭代的结果进行**归一化（normalization）**处理。

也就是说，我们需要对 $\vec{x}^{(k)}$ 进行归一化，使得 $\|\vec{x}^{(k)}\|_{\infty} = 1$。

令 $ \\|\vec{x}^{(k)}\\| _ {\infty} = | \vec{x}^{(k)} _ {p_k} | $，其中 $\vec{x}^{(k)}_{p_k}$ 是 $\vec{x}^{(k)}$ 中绝对值最大的分量。那么归一化后的向量为：
$$ \vec{u} ^ {(k)} = \frac{\vec{x} ^ {(k)}}{|\vec{x} ^ {(k)}_{p_k}|} $$

于是迭代的过程就变为了
$$ \vec{u} ^ {(k-1)} = \frac{\vec{x} ^ {(k-1)}}{|\vec{x} ^ {(k-1)} _ {p _ {k-1}}|}, \quad \vec{x} ^ {(k)} = A\vec{u} ^ {(k-1)} $$

$$ \Longrightarrow \quad \vec{u}^{(k)} = \frac{\vec{x}^{(k)}}{|\vec{x}^{(k)}_{p_k}|},\quad  \lambda_1 \approx \frac{\vec{x}^{(k)}_i}{\vec{u}^{(k-1)}_i} = \vec{x}^{(k)}_{p_{k-1}} $$

!!! note "幂法的伪代码"
    <figure markdown="span">
        ![](./assets/chap-9-1.png){width=85%}
    </figure>
    <figure markdown="span">
        ![](./assets/chap-9-2.png){width=85%}
    </figure>

!!! tip
    - 该方法在有多重特征值 $\lambda_1 = \lambda_2 = \cdots = \lambda_r$ 时同样有效，因为：

        $$ \vec{x}^{(k)} = \lambda_1^k \left[ \sum_{j=1}^{r} \beta_j \vec{v_j} + \sum_{j=r+1}^{n} \beta_j \left( \frac{\lambda_j}{\lambda_1} \right)^k \vec{v_j} \right] \approx \lambda_1^k \left( \sum_{j=1}^{r} \beta_j \vec{v_j} \right) $$

    - 若 $\lambda_1 = -\lambda_2$，该方法就会失效
    - 由于我们无法保证对于任意的初始近似向量 $\vec{x}^{(0)}$，都有对应的 $\beta_1 \neq 0$，因此该方法并不总是有效

        在这种情况下迭代得到的结果就可能不是 $\vec{v_1}$，而是第一个满足 $(\vec{x}^{(0)}, \vec{v_m}) \neq 0$ 的特征向量 $\vec{v_m}$，它对应的特征值为 $\lambda_m$，且 $|\lambda_m| < |\lambda_1|$。

    - 这里我们也可以使用 Aitken's $\Delta^2$ 加速法来加速收敛

### Rate of Convergence

在前面的计算中，我们已经得到：
$$ \vec{x} ^ {(k)} = A \vec{x} ^ {(k-1)} = \lambda_1 ^ k \sum_{j=2} ^ {n} \beta_j \left( \frac{\lambda_j}{\lambda_1} \right) ^ k \vec{v_j} $$

假设 $\lambda_1 > \lambda_2 \geqslant \lambda_3 \geqslant ... \geqslant \lambda_n$ 且 $|\lambda_2| > |\lambda_n| $，那么我们的目标就是让 $\left|\dfrac{\lambda_2}{\lambda_1}\right|$ 尽可能地小，这样收敛的速度就会更快

<figure markdown="span">
    ![](./assets/chap-9-3.png){width=75%}
</figure>

思路：令 $B = A - pI$，其中 $p = \dfrac{\lambda_2 + \lambda_n}{2}$，那么 $B$ 的特征值为 $\lambda_i - p$，其中 $i = 1, 2, ..., n$。

注意到 $\left|\dfrac{\lambda_2 - p}{\lambda_1 - p}\right| < \left|\dfrac{\lambda_2}{\lambda_1}\right|$，因此我们可以通过对矩阵 $B$ 使用幂法来加速收敛。

> 但是一般而言我们并不知道 $\lambda_2$ 和 $\lambda_n$ 的值，因此这种方法在实际应用中并不常用。

### Inverse Power Method

!!! info "反幂法"
    **反幂法（inverse power method）** 是幂法的一种改进方法，相比幂法能够更快地收敛。它可以用来确定矩阵 $A$ 中最接近于特定数 $q$ 的特征值，即找到 $\lambda_i$，使得 $|\lambda_i - q|$ 最小。

假设矩阵 $A$ 的特征值满足 $|\lambda_1| \geqslant |\lambda_2| \geqslant ... > |\lambda_n|$，那么相应的，矩阵 $A^{-1}$ 的特征值则满足 
$$ \left| \frac{1}{\lambda_n} \right| > \left|\frac{1}{\lambda_{n-1}}\right| \geqslant ... \geqslant \left|\frac{1}{\lambda_1}\right| $$

并且这些特征值会对应于相同的特征向量。

因此 $A^{-1}$ 的主特征值为 $\iff$ $A$ 的最小特征值。

如果我们想要计算 $\vec{x}^{(k+1)} = A^{-1} \vec{x}^{(k)}$，实际上我们并不需要计算 $A^{-1}$，而是可以通过解线性方程组 $A \vec{x}^{(k+1)} = \vec{x}^{(k)}$ 来得到 $\vec{x}^{(k+1)}$，其中需要把 $A$ 进行分解。

!!! tip "反幂法的思路"
    如果我们知道 $A$ 的一个特征值 $\lambda_i$ 是最接近于一个已知的特定值 $q$ 的，那么对于任意的 $j \neq i$，都有 $|\lambda_i - q| \ll |\lambda_j - q|$。

    如果 $(A - qI)^{-1}$ 存在，从上面的收敛速度分析中可以知道，反幂法能以更快的收敛速度找到 $(A - qI)^{-1}$ 的主特征值 $\dfrac{1}{\lambda_i - q}$，从而得到 $\lambda_i$

!!! note "反幂法的伪代码"
    <figure markdown="span">
        ![](./assets/chap-9-4.png){width=85%}
    </figure>
 