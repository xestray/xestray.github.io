---
    comments: true
    statistics: true
---

# 第一章 事件及其概率

## 1.1 随机现象与统计规律性

- **必然事件**：必然会发生的事件
- **不可能事件**：必然不会发生的事件
- **随机事件**：在一定条件下，不能准确预测其结果的事件

在相同条件下重复做 $N$ 次试验，各次试验互不影响，考察事件 $A$ 发生的次数（频数） $n$，称 $F_N(A)=\frac{n}{N}$ 为 $A$ 在 $N$ 次试验中发生的频率。
频率所稳定到的那个常数可以表示事件 $A$ 的概率，记为 $P(A)$。

!!! note "频率的性质"
    从频率的定义可以看出频率有以下性质：

    1. 非负性：$0\leq F_N(A)\leq 1$
    2. 规范性：对必然事件 $\Omega$，$F_N(\Omega)=\frac{N}{N}=1$
    3. 可列可加性：若 $A_1,A_2,\cdots$ 两两互斥，则 $F_N(A_1\cup A_2\cup\cdots)=F_N(A_1)+F_N(A_2)+\cdots$

## 1.2 古典概型

随机试验的每一基本结果称为**样本点**，记作 $\omega$，所有基本事件的全体称为**样本空间**，记为 $\Omega$。

!!! note "古典概型的特征"
    - 样本空间是有限的，$\Omega=\{\omega_1,\omega_2,\cdots,\omega_n\}$，其中 $\omega_i(I=1,2,\cdot，n)$ 是基本事件
    - 每个基本事件发生的可能性相同，即 $P(\omega_i)=\frac{1}{n}$

古典概率的定义：

- 设试验有 $n$ 个等可能的基本事件，而 $A$ 事件恰包含其中 $m$ 个基本事件，则 $P(A)$ 定义为

$$P(A)=\frac{m}{n}=\frac{A包含的样本点数}{样本空间中样本点总数}$$

!!! note "古典概率的性质"
    从古典概型的定义可以看出概率有以下性质：
 
    1. 非负性：$0\leq P(A)\leq 1$
    2. 规范性：$P(\Omega)=1$
    3. 可列可加性：若 $A_1,A_2,\cdots$ 两两互斥，则 $P(A_1\cup A_2\cup\cdots)=P(A_1)+P(A_2)+\cdots$

    特别地，若 $A$ 与 $B$ 两事件互反（不可能同时发生且至少发生一个），则 $P(A)=1 - P(B)$

??? example 
    设有 $n$ 个球，$N$ 个格子$(n \leq N)$，球和格子都是可以区分的。每个球落在各格子内的概率相同（设格子足够大，可以容纳任意多个球），将这 $n$ 个球随机地放入这 $N$ 个格子中，求：

    1. 指定的 $n$ 个格子中各有一个球的概率
    2. 有 $n$ 个格子各有一球的概率

    **解**：
    1. 
    \\[P(A) = \frac{n!}{N^n}\\]
    2. 
    $$
    \begin{aligned}
    P(B) &= \frac{P^n_N}{N^n} = \frac{N(N-1)\cdots(N-n+1)}{N^n} \\\\
    &= \left(1-\frac{1}{N}\right)\left(1-\frac{2}{N}\right)\cdots\left(1-\frac{n-1}{N}\right)
    \end{aligned} 
    $$
    注意到 $\log(1-x) = -x +O(x^2),x \rightarrow 0$. 我们有
    $$
    \begin{aligned}
    &\log \left(1-\frac{1}{N}\right)\left(1-\frac{2}{N}\right)\cdots\left(1-\frac{n-1}{N}\right)\\\\
    &= \sum_{i=1}^{n-1}\log\left(1-\frac{i}{N}\right) = -\sum_{i=1}^{n-1}\frac{i}{N} + O\left(\frac{1}{N^2}\right)\\\\
    &= -\frac{n(n-1)}{2N} + O\left(\frac{n^3}{N^2}\right)
    \end{aligned}
    $$
    故当 $N$ 比 $n$ 大得多时，可以采用近似计算公式 
    \\[P(B) \approx \exp\{1-\frac{n(n-1)}{2N}\}\\]

### 几何概率

若样本空间是一个包含无限个点的区域 $\Omega$（一维、二维或 $n$ 维），样本点是区域中的一个点，此时用点数度量样本点的多少就毫无意义。若记事件 $A_g = $ \{任取一个样本点，它落在区域 $g \subset \Omega$ \} ，则 $A_g$ 的概率定义为
$$ P(A_g) = \frac{g 的测度}{\Omega 的测度} $$
这样定义的概率称为**几何概率**。

## 1.3 概率的公理化定义

### 事件

- 事件一般用大写字母 $A,B,C,\cdots$ 表示，事件的概率用 $P(A),P(B),P(C),\cdots$ 表示。
- 样本空间 $\Omega$ 也可看作一个事件，$\Omega$ 是必然事件
- 类似地，把空集 $\emptyset$ 也看作一个事件，$\emptyset$ 是不可能事件

**事件的运算**

- $A$ **包含** $B$（$B$ 包含于 $A$）： $A \supset B(B \subset A)$
- $A$ 与 $B$ **相等**： $A = B$，即 $A \supset B$ 且 $B \supset A$
- $A$ 与 $B$ 的**并**： $A \cup B = \{ \omega | \omega \in A 或 \omega \in B \}$
- $A$ 与 $B$ 的**交**： $A \cap B = \{ \omega | \omega \in A 且 \omega \in B \}$
- $A$ 与 $B$ 的**差**： $A \backslash B = \{ \omega | \omega \in A 且 \omega \notin B \}$
- $A$ 与 $B$ **互不相容**（**互斥**）： $A \cap B = \emptyset$，在此时有时用 $A + B$ 代替 $A \cup B$
- $A$ 与 $B$ 不可能同时发生，且至少发生一个： $A \cup B = \Omega, A \cap B = \emptyset$，则称 $A$ 与 $B$ **互反**（互为逆事件、对立事件、余事件），记作 $B = A^c$ 或 $B = \overline{A}$

**运算性质**

- 交换律： $A \cup B = B \cup A, A \cap B = B \cap A$
- 结合律： $(A \cup B) \cup C = A \cup (B \cup C),~~ (A \cap B) $
- 分配律： $A \cup (B \cap C) = (A \cup B) \cap (A \cup C),~~ A \cap (B \cup C) = (A \cap B) \cup (A \cap C)$
- 对偶律（De Morgan律）： $\overline{A \cup B} = \overline{A} \cap \overline{B},~~ \overline{A \cap B} = \overline{A} \cup \overline{B}$
- $A~ \backslash ~B = A\overline{B}$

### 概率空间

概率空间包含三个要素。

第一个要素：样本空间 $\Omega$，是样本点 $\omega$ 的全体，根据问题需事先取定。

第二个要素：事件域 $\mathscr{F}$，是 $\Omega$ 中满足下列条件的子集的全体所组成的集合：

1. $\Omega \in \mathscr{F}$
2. 若 $A \in \mathscr{F}$，则 $\overline{A} \in \mathscr{F}$
3. 若 $A_1,A_2,\cdots, A_n, \cdots \in \mathscr{F}$，则 $\cup_{n=1}^{\infty}A_n \in \mathscr{F}$

满足这三个条件的集合 $\mathscr{F}$ 称为 $\Omega$ 上的 $\sigma$-代数或 $\sigma$-域。$\mathscr{F}$ 中的元素（$\Omega$ 的子集）称为事件。

由这三个条件，可以推得事件域有下列性质：

4. $\emptyset \in \mathscr{F}$（因为 $\overline{\Omega} = \emptyset$）
5. 若 $A_1,A_2,\cdots, A_n, \cdots \in \mathscr{F}$，则 $\cap_{n=1}^{\infty}A_n \in \mathscr{F}$（因为 $\overline{\cup_{n=1}^{\infty}A_n} = \cap_{n=1}^{\infty}\overline{A_n}$）
6. 若 $A_1,A_2,\cdots, A_n \in \mathscr{F}$，则 $\cup_{i=1}^{n}A_i \in \mathscr{F}$，$\cap_{i=1}^{n}A_i \in \mathscr{F}$

事件域也可根据问题选择，对同一样本空间 $\Omega$ 可能有不同的 $\sigma$-代数。

- 如最简单的事件域 $\mathscr{F}_1 = \{ \emptyset, \Omega \}$，称为平凡 $\sigma$-代数。
- 复杂的如 $\mathscr{F}_2 =$ \{ $\Omega$ 的一切子集 \}。

- 若 $\Omega$ 由有限或可列个样本点组成，则常取 $\Omega$ 的一切子集所成的集类作为 $\mathscr{F}$
- 若 $\Omega = \mathbf{R}$（一维实数全体），此时常取一切左开右闭有界区间和它们的（有限或可列）并、（有限或可列）交、逆所成的集的全体为 $\mathscr{F}$（通常记为 $\mathscr{B}$）称为一维波雷尔（Borel）$\sigma$-代数，其中的集称为一维波雷尔集，它是比全体区间大得多的一个集类。
- 若 $\Omega = \mathbf{R}^n$（$n$ 维实数全体），则常取一切左开右闭有界 $n$ 维矩形和它们的（有限或可列）并、（有限或可列）交、逆所成的集的全体为 $\mathscr{F}$（通常记为 $\mathscr{B}^n$），称为 $n$ 维波雷尔 $\sigma$-代数，其中的集称为 $n$ 维波雷尔集。

如果我们对 $\Omega$ 的某个子集类 $\mathscr{C}$ 感兴趣，所选的事件域 $\mathscr{F}$（可以是包含 $\mathscr{C}$ 的最小 $\sigma$-代数，这种 $\sigma$-代数是存在的，因为：

1. 至少有一个包含 $\mathscr{C}$ 的 $\sigma$-代数，即上述 $\mathscr{F}_2$
2. 若有很多包含 $\mathscr{C}$ 的 $\sigma$-代数，则它们的交也是 $\sigma$-代数，且就是最小的。

特别地，如果我们只对 $\Omega$ 的一个子集 $A$ 感兴趣，我们可以取包含 $A$ 的最小 $\sigma$-代数。
$$\mathscr{F} = \{ \emptyset, A, \overline{A}, \Omega \}$$

第三个要素：概率 $P$
!!! note "概率的定义"
    概率 $P$是定义在事件域 $\mathscr{F}$ 上的实值集函数，满足以下三个条件：

    1. 非负性：$P(A) \geq 0，A \in \mathscr{F}$
    2. 规范性：$P(\Omega) = 1$
    3. 可列可加性：若 $A_1,A_2,\cdots, A_n, \cdots \in \mathscr{F}$ 两两互斥，则 
    $$ P(\sum_{n=1}^{\infty}A_n) = \sum_{n=1}^{\infty}P(A_n) $$

用测度论的语言来说，概率 $P$ 是定义在 $\sigma$-代数上的规范化的测度。

三元体（$\Omega, \mathscr{F}, P$）称为概率空间。

**概率的运算公式**

- $P(\emptyset) = 0$
- 若 $A_i A_j = \emptyset, i,j=1, 2, \cdots, n, i \neq j$，则 
    $$P(\sum_{i=1}^nA_i) = \sum_{i=1}^nP(A_i)$$
- $P(\overline{A}) = 1 - P(A)$
- 若 $B \subset A$ 则 $P(A-B) = P(A) - P(B)$
- $P(A \cup B) = P(A) + P(B) - P(A \cap B)$
- $P(A \backslash B) = P(A) - P(AB)$
- (多还少补定理)

$$
\begin{aligned}
&~~~~P(A_1 \cup A_2 \cup \cdots \cup A_n)\\\\
&= \sum_{i=1}^nP(A_i) - \sum_{1 \leq i < j \leq n}P(A_iA_j) + \cdots + (-1)^{n-1}P(A_1A_2\cdots A_n)
\end{aligned}
$$

- （次可列可加性）

$$P(\cup_{n=1}^{\infty}A_n) \leq \sum_{n=1}^{\infty}P(A_n)$$

### 概率测度的连续性

给定概率空间 $(\Omega, \mathscr{F}, P)$，若 $A_1, A_2, \cdots$ 是一列单调增加的事件序列，即
$$A_1 \subset A_2 \subset \cdots$$ 
记 $A = \cup_{n=1}^{\infty}A_n$，称 $A$ 为事件序列 $A_n$ 的极限事件。从公理化定义可以看出，$A$ 仍然是一个事件。下面定理给出该事件的概率大小

!!! info "定理1.1"
    若 $A_1, A_2, \cdots$ 是一列单调增加的事件序列，则

    $$P(A) = \lim_{n \rightarrow \infty}P(A_n)$$

## 1.4 条件概率与事件的独立性

### 条件概率

设 $A,B$ 是两个事件，且 $P(B) > 0$，则在事件 $B$ 发生的条件下，事件 $A$ 发生的概率称为事件 $A$ 关于事件 $B$ 的**条件概率**，记作 $P(A|B)$












### 全概率公式与贝叶斯公式



### 事件独立性


