---
    comments: true
    statistics: true
    counter: true
---

# 第一章 事件及其概率

## 随机现象与统计规律性

- **必然事件**：必然会发生的事件
- **不可能事件**：必然不会发生的事件
- **随机事件**：在一定条件下，不能准确预测其结果的事件

在相同条件下重复做 $N$ 次试验，各次试验互不影响，考察事件 $A$ 发生的次数（频数） $n$，称 $F_N(A)=\frac{n}{N}$ 为 $A$ 在 $N$ 次试验中发生的频率。
频率所稳定到的那个常数可以表示事件 $A$ 的概率，记为 $P(A)$。

!!! note "频率的性质"
    从频率的定义可以看出频率有以下性质：

    1. 非负性：$0\leqslant F_N(A)\leqslant 1$
    2. 规范性：对必然事件 $\Omega$，$F_N(\Omega)=\frac{N}{N}=1$
    3. 可列可加性：若 $A_1,A_2,\cdots$ 两两互斥，则 $F_N(A_1\cup A_2\cup\cdots)=F_N(A_1)+F_N(A_2)+\cdots$

## 古典概型

随机试验的每一基本结果称为**样本点**，记作 $\omega$，所有基本事件的全体称为**样本空间**，记为 $\Omega$。

!!! note "古典概型的特征"
    - 样本空间是有限的，$\Omega=\{\omega_1,\omega_2,\cdots,\omega_n\}$，其中 $\omega_i(I=1,2,\cdot，n)$ 是基本事件
    - 每个基本事件发生的可能性相同，即 $P(\omega_i)=\frac{1}{n}$

古典概率的定义：

- 设试验有 $n$ 个等可能的基本事件，而 $A$ 事件恰包含其中 $m$ 个基本事件，则 $P(A)$ 定义为

$$P(A)=\frac{m}{n}=\frac{A包含的样本点数}{样本空间中样本点总数}$$

!!! note "古典概率的性质"
    从古典概型的定义可以看出概率有以下性质：
 
    1. 非负性：$0\leqslant P(A)\leqslant 1$
    2. 规范性：$P(\Omega)=1$
    3. 可列可加性：若 $A_1,A_2,\cdots$ 两两互斥，则 $P(A_1\cup A_2\cup\cdots)=P(A_1)+P(A_2)+\cdots$

    特别地，若 $A$ 与 $B$ 两事件互反（不可能同时发生且至少发生一个），则 $P(A)=1 - P(B)$

??? example 
    设有 $n$ 个球，$N$ 个格子$(n \leqslant N)$，球和格子都是可以区分的。每个球落在各格子内的概率相同（设格子足够大，可以容纳任意多个球），将这 $n$ 个球随机地放入这 $N$ 个格子中，求：

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

## 概率的公理化定义

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

    1. 非负性：$P(A) \geqslant 0，A \in \mathscr{F}$
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

$$\begin{aligned}
&~~~~P(A_1 \cup A_2 \cup \cdots \cup A_n)\\\\
&= \sum_{i=1}^nP(A_i) - \sum_{1 \leqslant i < j \leqslant n}P(A_iA_j) + \cdots + (-1)^{n-1}P(A_1A_2\cdots A_n)
\end{aligned}$$

- （次可列可加性）

$$P(\cup_{n=1}^{\infty} A_n) \leqslant \sum_{n=1}^{\infty}P(A_n)$$

### 概率测度的连续性

给定概率空间 $(\Omega, \mathscr{F}, P)$，若 $A_1, A_2, \cdots$ 是一列单调增加的事件序列，即 

$$ A_1 \subset A_2 \subset \cdots \subset A_n \subset \cdots $$ 

记 $A = \bigcup_{n=1}^{\infty} A_n$，称 $A$ 为事件序列 $A_n$ 的极限事件。从公理化定义可以看出，$A$ 仍然是一个事件。下面定理给出该事件的概率大小

!!! info "定理1.1"
    若 $A_1, A_2, \cdots$ 是一列单调增加的事件序列，具有极限 $A$，则

    $$P(A) = \lim_{n \to \infty}P(A_n)$$

对一列单调增加的事件 $ A_1 \subset A_2 \subset \cdots $，记 $\lim \limits_{n \to \infty} A_n = \bigcup_{n=1}^{\infty} A_n$，则上述定理说明
$$ P(\lim_{n \to \infty} A_n) = \lim_{n \to \infty} P(A_n) $$
所以我们说概率具有连续性。

如果 $ A_1 \subset A_2 \subset \cdots $ 是一个单调减少的事件序列，记 $\lim \limits_{n \to \infty} A_n = \bigcap_{n=1}^{\infty} A_n$，那么同样有
$$  P(\lim_{n \to \infty} A_n) = \lim_{n \to \infty} P(A_n) $$
为了进行区分，我们将前者称为概率的上连续性，后者称为概率的下连续性。

#### 事件的上极限和下极限

事件是样本点的集合，事件的上极限和下极限就是这个样本点集合的极限。

对于一个事件序列，我们可以更一般地定义它的上极限和下极限

!!! note "事件的上极限和下极限"
    对于事件序列 $ \{ A_n \}_{n=1}^{\infty} = A_1 ,\enspace A_2,  \cdots $，我们定义
    
    $$ \limsup_{n \to \infty} A_n = \bigcap_{n=1}^{\infty} \bigcup_{k=n}^{\infty} A_k $$

    为它的上极限， 
    $$ \liminf_{n \to \infty} A_n = \bigcup_{n=1}^{\infty} \bigcap_{k=n}^{\infty} A_k $$

    为它的下极限。

    容易验证 $ \liminf \limits_{n \to \infty} A_n \subset \limsup \limits_{n \to \infty} A_n$

    如果 $ \liminf \limits_{n \to \infty} A_n = \limsup \limits_{n \to \infty} A_n$，就称 $A_n$ 的极限存在，并记作 $\lim \limits_{n \to \infty} A_n$

- 事件序列的上极限是事件的**先并后交**，可以认为是所有并集的交
- 事件序列的下极限是事件的**先交后并**，可以认为是所有交集的并

## 条件概率与事件的独立性

### 条件概率

设 $A,B$ 是两个事件，且 $P(B) > 0$，则在事件 $B$ 发生的条件下，事件 $A$ 发生的概率称为事件 $A$ 关于事件 $B$ 的**条件概率**，记作 $P(A|B)$

在某种情况下，条件的附加意味着对样本空间的压缩，相应的概率可在压缩的样本空间内直接计算。

!!! note "定义"
    对任意事件 $A$ 和 $B$，若 $P(B) \neq 0$，则“在事件 $B$ 发生的条件下 $A$ 发生的条件概率”，记作 $P(A|B)$，定义为
    $$P(A|B) = \frac{P(AB)}{P(B)}$$
    反过来也可用条件概率表示 $A, B$ 的乘积概率，即有**乘法公式**
    $$ P(AB) = P(A|B)P(B) , P(B) \neq 0 $$
    或
    $$ P(AB) = P(B|A)P(A) , P(A) \neq 0 $$

### 全概率公式与贝叶斯公式

!!! note "定义1.3"
    若事件列 $ \\{ A_1, A_2 \cdots, A_n \cdots \\} $ 满足下列两个条件

    1. $A_i, i=1, 2, \cdots $ 两两互不相容，且 $P(A_i) > 0$
    2. $\sum_{i=1}^{\infty}A_i = \Omega$

    则称 $ \{ A_1, A_2 \cdots, A_n \cdots \} $ 为 $\Omega$ 的一个**完备事件组**，也称为 $\Omega$ 的一个**分割**。

- 最简单的完备事件组是 $\{ A, \overline{A}\}$

!!! info "定理1.2（全概率公式）"
    设 $ \{ A_1, A_2 \cdots, A_n \cdots \} $ 是 $\Omega$ 的一个完备事件组，$B$ 是任一事件，则
    $$ P(B) = \sum_{i=1}^{\infty}P(A_i)P(B|A_i) $$

- 概率公式的意义在于，它把事件 $B$ 的概率 $P(B)$ 分解为若干个条件概率的乘积之和。

!!! info "贝叶斯公式"
    设 $ \\{ A_1, A_2 \cdots, A_n \cdots \\} $ 是 $\Omega$ 的一个完备事件组，$B$ 是任一事件，且 $P(B) > 0$，则
    $$ P(A_i|B) = \frac{P(A_i)P(B|A_i)}{\sum_{k=1}^{\infty}P(A_k)P(B|A_k)} $$

- $P(A_i)$ 是没有进一步的信息（不知道 $B$ 是否发生）时，对 $A_i$ 概率的估计，称为**先验概率**
- $P(A_i|B)$ 是在事件 $B$ 发生后对 $A_i$ 发生概率的估计，称为**后验概率**

- 全概率公式：“由原因推结果”
- 贝叶斯公式：“由结果推原因”

### 事件独立性

#### 两个事件的独立性

- 当 $P(A|B) = P(A)$ ,即 $P(AB) = P(A)P(B)$ 时，称事件 $A$ 与事件 $B$ **统计独立**，或 $A$ 与 $B$ 独立，记作 $A \perp B$。
- 当 $A$ 与 $B$ 不独立时，也称 $A$ 与 $B$ **统计相依**。

??? example "例题"
    === "例一"
    求证：若 $A$ 与 $B$ 互不相容，且 $P(A)P(B) \neq 0$，则 $A$ 与 $B$ 不独立。
    证明：$A$ 与 $B$不相容，则 $P(AB) = 0 \neq P(A)P(B)$，故 $A$ 与 $B$ 不独立。

    === "例二"
    已知 $A$ 与 $B$ 独立，求证 $A$ 与 $\overline{B}$，$\overline{A}$ 与 $B$，$\overline{A}$ 与 $\overline{B}$ 也独立。
    证明：由 $A$ 与 $B$ 独立，有 $P(AB) = P(A)P(B)$，从而
    $$
    \begin{aligned}
    P(A\overline{B}) &= P(A) - P(AB) = P(A) - P(A)P(B) \\\\
    &= P(A) - P(A)P(B) = P(A)(1-P(B)) \\\\
    &= P(A)P(\overline{B}) \\\\
    \end{aligned}
    $$

#### 多个时间的独立性

!!! note "定义1.5"
    若 
    $$
    \begin{cases}
    P(AB) = P(A)P(B) \\\\
    P(AC) = P(A)P(C) \\\\
    P(BC) = P(B)P(C)
    \end{cases}
    $$
    且 $P(ABC) = P(A)P(B)P(C)$ 则称事件 $A, B, C$ **相互独立**。

!!! note "定义1.6"
    若对一切可能的组合 $1 \leqslant i < j < k < \cdots \leqslant n$，有
    $$ 
    \begin{cases}
    P(A_iA_j) = P(A_i)P(A_j)\\\\
    P(A_iA_jA_k) = P(A_i)P(A_j)P(A_k)\\\\
    \cdots \\\\
    P(A_iA_jA_k\cdots A_n) = P(A_i)P(A_j)P(A_k) \cdots P(A_n)
    \end{cases}
    $$
    就称 $A_1, A_2, \cdots, A_n$ **相互独立**。

#### 试验的独立性

与事件的独立性密切相关的是随机试验的独立性。

- 一般来说，若有 $n$ 个试验 $E_1, E_2, \cdots, E_n$，每个试验的每个结果都是一个事件。如果 $E_1$ 的任一事件都与 $E_2$ 的任一事件与 $\cdots$ 与 $E_n$ 的任一事件相互独立，就说 $E_1, E_2, \cdots, E_n$ 相互独立
- 记 $E_i$ 的样本空间为 $\Omega_i$ 为描述这 $n$ 次试验，要构造复合试验 $E = (E_1, E_2, \cdots, E_n)$ 对应的样本空间为 $\Omega = \Omega_1 \times \Omega_2 \times \cdots \times \Omega_n$ 是 $n$ 个样本空间的直积
- $E$ 中的样本点 $\omega = (\omega^{(1)}, \cdots, \omega^{(n)})$，其中 $\omega^{(i)} \in \Omega_i$

若 $A^{(i)}$ 为 $E_i$ 的任一事件，对一切 $A^{(1)}, A^{(2)}, \cdots, A^{(n)}$ 均有
$$ P(A^{(1)}A^{(2)} \cdots A^{(n)}) = P(A^{(1)})P(A^{(2)}) \cdots P(A^{(n)})$$

#### 伯努利概型 

- 若一次随机试验$E$只有$A$与$\overline{A}$两种相反的结果（是与否，成功与失败等），就称其为**伯努利试验**。对于$n$次重复独立的伯努利试验，这种概率模型称为**伯努利概型**。

二项分布：$n$ 次伯努利试验中事件 $A$ 恰好发生 $k$ 次的概率为（设 $A$ 的概率为 $p$，$\overline{A}$ 发生的概率为 $q$ ）
$$ b(k, n, p) = \binom{n}{k} p^k q^{n-k} = \frac{n!}{k!(n-k)!} p^k q^{n-k}, k=0, 1, \cdots, n $$

## 补充与注记

从$n$个不同物件中取$k$个$(1 \leqslant r \leqslant n)$的不同排列总数为
$$ P^r_n = n(n-1)(n-2) \cdots (n-r+1) $$

- 特别的，若$r=n$，有 $P^r_r = r(r-1) \cdots 1 = r!$，将其称为全排列。
- 人们常约定把 $0!$ 作为 $1$。

从$n$个不同物件中取$k$个$(1 \leqslant r \leqslant n)$的不同排列总数为
$$ \binom{n}{r} = \frac{P^r_n}{r!} = \frac{n!}{r!(n-r)!} $$

组合数也常称为二项式系数
$$ (a+b)^n = \sum^n_{i=0} \binom{n}{i} a^i b^{n-i} $$

一些常用的公式：
$$ \binom{n}{0} + \binom{n}{1} + \cdots + \binom{n}{n} = 2^n $$
$$ \binom{n}{0} - \binom{n}{1} + \cdots + (-1)^n \binom{n}{n} = 0 $$
$$ \binom{n}{k-1} + \binom{n}{k} = \binom{n+1}{k} $$
$$ \binom{m+n}{k} = \sum_{i=0}^k \binom{m}{i}\binom{n}{k-i} $$
$$ \sum_{i=0}^n \binom{n}{i} \binom{n}{i} = \binom{2n}{n} $$