---
    comments: true
    statistics: true
---

# Chap 7: Iterative Techniques in Matrix Algebra

假设我们要求解线性方程组 $A\vec{x} = \vec{b}$，其中 $A$ 是一个大型稀疏矩阵。直接方法（如高斯消去法）在这种情况下可能效率低下且耗费大量内存。

此时我们可以通过迭代方法来近似求解该方程，思路类似于用定点迭代法求解方程，我们可以把方程 $A\vec{x} = \vec{b}$ 重写为 
$$ \vec{x} = T\vec{x} + \vec{c} $$ 
因此我们就可以通过式子 $\vec{x}^{(k+1)} = T\vec{x}^{(k)} + \vec{c}$ 来迭代求解 $\vec{x}$ 的近似值。

## Norms of Vectors and Matrices

### Vector Norms

!!! definition "向量范数"
    $\mathbf{R}^n$ 上的向量范数是一个函数 $||\cdot||: \mathbf{R}^n \rightarrow \mathbf{R}$，满足以下性质：

    1. 非负性：$||\vec{x}|| \geq 0;\ ||\vec{x}|| = 0 \iff \vec{x} = \vec{0} \quad (\vec{x} \in \mathbf{R}^n)$
    2. 齐次性：$||\alpha \vec{x}|| = |\alpha| \cdot ||\vec{x}|| \quad (\alpha \in \mathbf{R},\ \vec{x} \in \mathbf{R}^n)$
    3. 三角不等式：$||\vec{x} + \vec{y}|| \leq ||\vec{x}|| + ||\vec{y}|| \quad (\vec{x}, \vec{y} \in \mathbf{R}^n)$

!!! note "常见的向量范数"
    - **1-范数**：$||\vec{x}||_1 = \sum_{i=1}^{n} |x_i|$
    - **2-范数**（欧几里得范数）：$||\vec{x}||_2 = \sqrt{\left( \sum\limits_{i=1}^{n} |x_i|^2 \right)}$
    - **p-范数**：$||\vec{x}||_p = \left( \sum\limits_{i=1}^{n} |x_i|^p \right)^{\frac{1}{p}} \quad (p \geq 1)$
    - **无穷范数**：$||\vec{x}||_\infty = \max\limits_{1 \leq i \leq n} |x_i|$

!!! definition "向量的收敛性"
    我们称 $\mathbf{R}^n$ 上的向量序列 $\{\vec{x}^{(k)}\}_{k=1}^{+\infty}$ 依照范数 $||\cdot||$ 收敛于 $\vec{x} \in \mathbf{R}^n$，当且仅当对于任意的 $\varepsilon > 0$，存在正整数 $N$，使得当 $k > N$ 时，有

    $$ ||\vec{x}^{(k)} - \vec{x}|| < \varepsilon $$

!!! theorem 
    对于无穷范数 $||\cdot||_\infty$，向量序列 $\{\vec{x}^{(k)}\}_{k=1}^{+\infty}$ 收敛于 $\vec{x} \in \mathbf{R}^n$ 的充分必要条件是对于每个分量 $i = 1, 2, \ldots, n$，数列 $\{x_i^{(k)}\}_{k=1}^{+\infty}$ 收敛于 $x_i$。

    即

    $$ \lim_{k \to +\infty} \vec{x}^{(k)} = \vec{x} \iff \lim_{k \to +\infty} x_i^{(k)} = x_i,\quad i = 1, 2, \ldots, n $$

!!! definition "向量范数的等价性"
    在 $\mathbf{R}^n$ 上的两个向量范数 $||\cdot||_\alpha$ 和 $||\cdot||_\beta$ 称为等价的，如果存在正常数 $c_1, c_2 > 0$，使得对于任意 $\vec{x} \in \mathbf{R}^n$，都有

    $$ c_1 ||\vec{x}||_\alpha \leq ||\vec{x}||_\beta \leq c_2 ||\vec{x}||_\alpha $$

!!! tip 
    事实上，$\mathbf{R}^n$ 上的任意两个向量范数都是等价的。

??? example "$||\cdot||_2$ 和 $||\cdot||_\infty$ 是等价的"
    假设 $\vec{x}$ 中最大的一个分量为 $|x_m| = \max_{1 \leq i \leq n} |x_i|$，那么

    $$ ||\vec{x}||_\infty = |x_m| $$
        
    我们知道

    $$ ||\vec{x}||_2 = \sqrt{\sum_{i=1}^{n} |x_i|^2} \geq ||\sqrt{|x_m|^2}|| = |x_m| = ||\vec{x}||_\infty $$

    并且我们还有

    $$ ||\vec{x}||_2 = \sqrt{\sum_{i=1}^{n} |x_i|^2} \leq \sqrt{\sum_{i=1}^{n} |x_m|^2} = \sqrt{n|x_m|^2} = \sqrt{n} ||\vec{x}||_\infty $$

    所以 $ ||\vec{x}||_\infty \leq ||\vec{x}||_2 \leq \sqrt{n} ||\vec{x}||_\infty $，即 $c_1 = 1$，$c_2 = \sqrt{n}$，从而 $||\cdot||_2$ 和 $||\cdot||_\infty$ 是等价的。

### Matrix Norms

!!! definition "矩阵范数"

    在 $\mathbf{R}^{n \times n}$ 上的矩阵范数是一个实值函数 $||\cdot||: \mathbf{R}^{n \times n} \rightarrow \mathbf{R}$，满足以下性质：

    1. 非负性：$||A|| \geq 0;\ ||A|| = 0 \iff A = 0$
    2. 齐次性：$||\alpha A|| = |\alpha| \cdot ||A|| \quad (\alpha \in \mathbf{R})$
    3. 三角不等式：$||A + B|| \leq ||A|| + ||B||$
    4. 乘法兼容性：$||AB|| \leq ||A|| \cdot ||B||$
    5. 一致性：$||A\vec{x}|| \leq ||A|| \cdot ||\vec{x}|| \quad (\vec{x} \in \mathbf{R}^n)$

    我们通过矩阵范数定义两个矩阵之间的距离：$d(A, B) = ||A - B||$。

    !!! note "常见的矩阵范数"





<figure markdown="span">
    ![](./assets/lec-4-1.png){width=75%}
</figure>
















































































































