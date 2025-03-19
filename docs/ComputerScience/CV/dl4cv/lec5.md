---
    comments: true
    statistics: true
---

# Backpropagation

## Backprop with scalars

### Conputational Graphs

直接在纸上计算梯度显然不是个好方法，因为它既难以计算，也无法实现模块化设计，因此我们可以转而使用**Computational Graphs**。

- **Computational Graphs** are a way to represent the flow of data through a network of operations.

<figure>
    <img src="../assets/计算图.png" width="80%">
</figure>

在计算图中，每个节点代表一个操作，每个边代表一个数据流。我们可以借助计算图和链式法则来反向传播地计算函数的梯度。

主要的步骤如下：

1. **Forward Pass**: 从输入节点开始，按照计算图的拓扑顺序计算每个节点的值。
2. **Backward Pass**: 从输出节点开始，按照计算图的拓扑逆序计算每个节点的梯度（导数）。

利用反向传播机制来计算梯度的好处在于，每一个小点都只需要关心自己的导数，而不需要关心其他任何节点的导数。在这之后我们只需要把这些数据汇总起来，就可以得到整个函数的梯度，这也是 **modular design** 的一个体现。

<figure>
    <img src="../assets/反向传播计算图.png" width="80%">
</figure>

现在考虑计算图中的一个结点，它接受两个输入 $x$ 和 $y$，输出 $z$，假设我们已经通过反向传播计算出了损失函数 $L$ 对 $z$ 的梯度 $\dfrac{\partial L}{\partial z}$，那么我们在计算 $z$ 对 $x$ 和 $y$ 的梯度后，就可以利用链式法则求出 $L$ 对 $x$ 和 $y$ 的梯度。即
$$ \dfrac{\partial L}{\partial x} = \dfrac{\partial z}{\partial x} \dfrac{\partial L}{\partial z} $$

$$ \dfrac{\partial L}{\partial y} = \dfrac{\partial z}{\partial y} \dfrac{\partial L}{\partial z} $$

其中，$\dfrac{\partial L}{\partial z}$ 称为 **upstream gradient**，$\dfrac{\partial z}{\partial x}$ 和 $\dfrac{\partial z}{\partial y}$ 称为 **local gradient**，$\dfrac{\partial L}{\partial x}$ 和 $\dfrac{\partial L}{\partial y}$ 称为 **downstream gradient**。

> 相当于把最终的损失函数看成河流的上游，我们只需要沿着河流的方向，一步一步地向下游计算每个结点的梯度，就可以得到整个函数的梯度。

!!! note "local gradient for common functions   "
    <figure>
        <img src="../assets/反向传播计算图sigmoid函数.png" width="80%">
    </figure>

    对于 sigmoid、tanh、ReLU 等常见的激活函数，我们可以把它们的计算过程当成一个结点，直接一次性计算它们的导数，不需要在函数内部一步一步地计算。

<figure>
    <img src="../assets/梯度流的组件.png" width="70%">
</figure>

- **Add Gate**: $\dfrac{\partial f}{\partial x} = \dfrac{\partial f}{\partial y} = 1$

    上游梯度会被直接赋给下游梯度，不会有任何变化。

- **Copy Gate**: 某种程度上是 add gate 的对偶，它的输出是输入的多分拷贝；它的下游梯度是所有上游梯度的和。
- **Mul Gate**: $\dfrac{\partial f}{\partial x} = y, \dfrac{\partial f}{\partial y} = x$

    乘法门的下游梯度是上游梯度乘以另一个输入。

- **Max Gate**: 最大输入的下游梯度等于上游梯度，其他输入的下游梯度为 0。

### Backprop Implementation

- 计算 forward pass 时，我们需要计算并记录每个中间变量的值
- 计算 backward pass 时，我们需要计算并记录损失函数对每个中间变量的梯度。

<figure>
    <img src="../assets/反向传播代码实现.png" width="70%">
</figure>

需要注意的是，我们在计算 backward pass 时，需要做的第一件事就是确定 base case，即最终输出相对于它自己的梯度（一定是 1）。在具体实现时，这一行可能会被省略，但我们始终需要考虑 base case 是什么。

> Your gradient code should look like a “reversed version” of your forward pass!

上面这种实现方法称为 "Flat" Backpropagation，它的优点在于简单直观，但缺点在于不够模块化。

我们也可以使用更为模块化的方法：Modular API。例如我们额可以对每一个类型的结点定义一个类，每个类都有一个 `forward` 和一个 `backward` 方法，这样我们就可以更好地实现模块化设计。

!!! example "PyTorch Autograd Functions"

    <figure>
        <img src="../assets/模块化反向传播.png" width="70%">
    </figure>

## Backprop with Vectors and Matrices

简单回顾一下数学知识：

- for $x \in \mathbb{R},\ y \in \mathbb{R}$，we have $\dfrac{\partial y}{\partial x} \in \mathbb{R}$
- for $x \in \mathbb{R}^n,\ y \in \mathbb{R}$，we have $\dfrac{\partial y}{\partial x} \in \mathbb{R}^n$, $ \left( \dfrac{\partial y}{\partial x} \right)_i = \dfrac{\partial y}{\partial x_i} $
- for $x \in \mathbb{R}^n,\ y \in \mathbb{R}^m$，we have $\dfrac{\partial y}{\partial x} \in \mathbb{R}^{n \times m}$, $ \left( \dfrac{\partial y}{\partial x} \right)_{i,j} = \dfrac{\partial y_j}{\partial x_i} $

输出结果相对于输入变量的梯度表示的是当这个输入变量的值发生微小变化时，输出结果会发生多大的变化，因而我们可以知道，对这个输入变量求偏导的结果一定是一个和输入变量同样形状的矩阵。Jacobian 矩阵的每一行表示输出结果的各个分量相对于输入变量的其中一个分量的梯度。

例如当 $x = (x_1, x_2),\ y = (y_1, y_2. y_3)$ 时，我们可以得到一个 $2 \times 3$ 的 Jacobian 矩阵。

$$ \dfrac{\partial y}{\partial x} = \begin{bmatrix} \dfrac{\partial y_1}{\partial x_1} & \dfrac{\partial y_1}{\partial x_2} \\\\ \dfrac{\partial y_2}{\partial x_1} & \dfrac{\partial y_2}{\partial x_2} \\\\ \dfrac{\partial y_3}{\partial x_1} & \dfrac{\partial y_3}{\partial x_2} \end{bmatrix} $$

!!! tip
    注意这里的 Jacobian 矩阵与数学分析中学到的 Jacobian 矩阵有所不同。
    
    假如 $y$ 是 $m$ 维向量，$x$ 是 $n$ 维向量，那么 $\dfrac{\partial L}{\partial y}$ 是一个 $m$ 维向量，$\dfrac{\partial L}{\partial x}$ 是一个 $n$ 维向量.

    我们在**数学**中常见到的链式法则是 
    $$ \dfrac{\partial L}{\partial x} = \dfrac{\partial L}{\partial y} \cdot \dfrac{\partial y}{\partial x} $$
    那么 Jacobian 矩阵 $\dfrac{\partial y}{\partial x}$ 就应该是 $m \times n$ 的 。

    而我们在**反向传播**中使用的链式法则是
    $$ \dfrac{\partial L}{\partial x} = \dfrac{\partial y}{\partial x} \cdot \dfrac{\partial L}{\partial y} $$
    那么 Jacobian 矩阵 $\dfrac{\partial y}{\partial x}$ 就应该是 $n \times m$ 的。

    这是因为在反向传播中，我们是从损失函数开始，逐步向前计算梯度，而在数学中，我们是从输入变量开始，逐步向后计算梯度。同时这里的乘法是矩阵乘法，因此 Jacobian 矩阵的形状也会根据两个向量 $x$ 和 $y$ 的维度而变化。

### Backprop with Vectors

!!! example "Backprop with Vectors"

    <figure>
        <img src="../assets/向量形式反向传播计算图.png" width="70%">
    </figure>

    在这一个节点中，输入 $x$ 和 $y$ 分别是维度为 $D_x$ 和 $D_y$ 的向量，输出 $z$ 是一个维度为 $D_z$ 的向量。那么根据我们刚刚提到的链式法则，Jacobian 矩阵 $\dfrac{\partial z}{\partial x}$ 和 $\dfrac{\partial z}{\partial y}$ 的形状分别是 $D_x \times D_z$ 和 $D_y \times D_z$ 的。

<figure>
    <img src="../assets/向量反向传播.png" width="70%">
</figure>

这里我们采用 ReLU 作为激活函数，假设我们已经从上游梯度中知道了损失函数关于 ReLU 函数的输出 $y$ 的梯度 $\dfrac{\partial L}{\partial y}$，那么我们就可以利用 Jacobian 矩阵计算出损失函数关于 ReLU 函数的输入 $x$ 的梯度 $\dfrac{\partial L}{\partial x}$。

但是这里存在一个问题：在这里，Jacobian 矩阵是一个非常非常稀疏的矩阵（对角矩阵），而我们在利用 Jacobian 矩阵计算的过程中需要计算所有的元素，这会带来大量的无效操作。尤其是在高维情况下，低效的同时也会导致占据大量的内存，甚至由于矩阵过大而无法计算。

那么我们需要一些技巧来提高计算的效率。对于 ReLU 函数，我们可以考虑把使用 Jacobian 矩阵相乘的方法替换为

$$ \left( \dfrac{\partial L}{\partial x} \right)_i = \begin{cases} \left( \dfrac{\partial L}{\partial y} \right)_i & \text{if } x > 0 \\\\ 0 & \text{if } x \leq 0 \end{cases} $$

这样我们就可以直接从上游梯度中得到下游梯度，而不需要计算 Jacobian 矩阵。

### Backprop with Matrices

<figure>
    <img src="../assets/矩阵形式反向传播计算图.png" width="70%">
</figure>

当输入的变元和输出的结果都是矩阵时，Jacobian 矩阵将会是一个 4 维的张量（tensor），这很难直接处理，因此我们也需要一些技巧来简化计算。

我们现在考虑两个矩阵相乘作为一个结点，输入 $X$ 和 $W$ 分别是 $N \times D$ 和 $D \times M$ 的矩阵，输出 $Y$ 是 $N \times M$ 的矩阵。

<figure>
    <img src="../assets/矩阵相乘示例.png" width="70%">
</figure>

显然 Jacobian 矩阵 $\dfrac{\partial y}{\partial x}$ 和 $\dfrac{\partial y}{\partial W}$ 的形状分别是 $(N \times D) \times (N \times M)$ 和 $(D \times M) \times (N \times M)$ 的，当维度很大时，这个 Jacobian 矩阵将会变得巨大无比，以至于无法在计算机内存中处理。

我们可以简单粗暴地逐元素计算梯度，首先计算 $y$ 对 $x_{1,1}$ 的梯度

<figure>
    <img src="../assets/逐元素计算梯度1.png" width="70%">
</figure>

计算得到的结果是

$$ \dfrac{\partial y}{\partial x_{1,1}} = 
\begin{bmatrix} 
3 & 2 & 1 & -1 \\
0 & 0 & 0 & 0 \\
\end{bmatrix} $$

接着我们可以利用它来计算损失函数对 $x_{1,1}$ 的梯度

$$ \begin{aligned}
\dfrac{\partial L}{\partial x_{1,1}} &= \dfrac{\partial y}{\partial x_{1,1}} \cdot \dfrac{\partial L}{\partial y} \\\\
&= (w_{1,:}) \cdot \dfrac{\partial L}{\partial y_{1,:}} \\\\
&= 3 * 2 + 2 * 3 + 1 * (-3) + (-1) * 9 \\\\
&= 0
\end{aligned} $$

以此类推我们就可以计算出损失函数对 $x$ 中每一个元素的梯度，汇总起来就是损失函数对 $x$ 的梯度。

$$ \begin{aligned}
\dfrac{\partial L}{\partial x_{i,j}} &= \dfrac{\partial y}{\partial x_{i,j}} \cdot \dfrac{\partial L}{\partial y} \\\\
&= (w_{j,:}) \cdot \dfrac{\partial L}{\partial y_{i,:}} 
\end{aligned} $$

!!! tip
    这里的 $ (w_{j,:}) \cdot \dfrac{\partial L}{\partial y_{i,:}} $ 表示的是 $w$ 的第 $j$ 行和 $\dfrac{\partial L}{\partial y}$ 的第 $i$ 行的点积。

我们还可以把这个计算过程进一步优化，利用矩阵乘法的性质，我们可以直接计算
$$ \dfrac{\partial L}{\partial x} = \dfrac{\partial L}{\partial y} w^T $$
这看起来好像很奇怪，但我们可以注意到

- $\dfrac{\partial L}{\partial y}$ 是 $N \times M$ 的矩阵
- $w^T$ 是 $M \times D$ 的矩阵
- $\dfrac{\partial L}{\partial x}$ 是 $N \times D$ 的矩阵，和输入矩阵 $X$ 的形状相同，是我们希望得到的矩阵

只有这一种相乘的方式才可以得到与我们目标形状相同的矩阵，这是一种便于记忆的小技巧。

同理，我们也可以知道 $L$ 关于 $W$ 的梯度是 $\dfrac{\partial L}{\partial W} = X^T \dfrac{\partial L}{\partial y}$。

!!! example
    <figure>
        <img src="../assets/矩阵反向传播计算技巧1.png" width="70%">
    </figure>

    这里当我们想要求出 $L$ 关于 $x_0$ 的梯度时，由于 $L$ 是一个标量，我们可以利用矩阵乘法的结合性，从右往左计算，这样每一次的乘法就都是矩阵与向量相乘，从而避免了矩阵与矩阵相乘，大大减少了计算量。

    <figure>
        <img src="../assets/矩阵反向传播计算技巧2.png" width="70%">
    </figure>

    由于起始输入 $a$ 是一个标量，我们可以从左往右做乘法，同样也可以避免矩阵与矩阵相乘。

进一步来说，我们还可以利用反向传播来计算高阶导数

<figure>
    <img src="../assets/反向传播计算高阶导数.png" width="70%">
</figure>

例如这里我们想要计算 $\dfrac{\partial^2 L}{\partial x^2}$，它是一个 $D_0 \times D_0$ 的 Hessian 矩阵，我们可考虑把它与一个向量 $v$ 相乘，并且我们知道当 $v$ 与 $x$ 无关时，有
$$ \dfrac{\partial^2 L}{\partial x^2} v = \dfrac{\partial}{\partial x} \left( \dfrac{\partial L}{\partial x} \cdot v \right) $$

那么我们就可以借助上图中的计算过程来反向求出 $\dfrac{\partial^2 L}{\partial x^2}$。

例如我们规定正则化项的惩罚函数是梯度的范数
$$ R(W) = \left\Vert \dfrac{\partial L}{\partial W} \right\Vert_2^2 = \left( \dfrac{\partial L}{\partial W} \right) \cdot \left( \dfrac{\partial L}{\partial W} \right) $$
那么我们就可以利用反向传播来计算正则化项的梯度
$$ \dfrac{\partial R}{\partial x_0} = 2 \left(\dfrac{\partial^2 L}{\partial x_0^2}\right) \left(\dfrac{\partial L}{\partial x_0}\right) $$




