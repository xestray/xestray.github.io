---
    comments: true
    statistics: true
---

# Regularization + Optimization

## Regularization

A model is **overfit（过拟合）** when it performs too well on the training data, and has poor performance for unseen data.

所谓正则化（regularization）的目的就是为了防止模型过拟合，使模型在训练集上表现良好的同时也能具有一定的泛化能力。

<figure>
    <img src="../assets/正则化1.png" width="70%">
</figure>

我们原先定义的损失为
$$ L(W) = \frac{1}{N} \sum_i L_i(f(x_i, W), y_i) $$
如果一直追求让损失函数的值最小，那么每一次训练后模型都会不断趋近于让训练集上的损失更小，这可能会导致模型反而对于训练集过于敏感，从而导致过拟合。

这时我们可以引入一个正则化项 $\lambda R(W)$，其中 $R(W)$ 是一个关于权重矩阵 $W$ 的函数，它与任何数据都无关，$\lambda$ 是一个超参数，用来控制正则化项的影响程度。

于是我们的新损失函数就变成了
$$ L(W) = \frac{1}{N} \sum_i L_i(f(x_i, W), y_i) + \lambda R(W) $$
其中第一项称为数据损失（data loss），第二项称为正则化损失（regularization loss）。

**Purpose of Regularization**:

- Express preferences in among models beyond ”minimize training error”
- Avoid overfitting: Prefer simple models that generalize better
- Improve optimization by	adding	curvature

!!! example

    <figure>
        <img src="../assets/正则化例子1.png" width="70%">
    </figure>

    假设我们现在有两个表现一致的权重矩阵 $w_1$ 和 $w_2$，假如没有正则化项，我们将无法决定应该采用哪一个。
    
    这时我们引入了 L2 正则化项，它的定义为
    $$ R(W) = \sum_k \sum_l W_{k, l}^2 $$
    那么我们很容易知道，$w_1$ 的正则化项的值要比 $w_2$ 的大，因此我们就可以从这两者中选出 $w_2$ 作为我们的最终模型。

!!! example
    现在假设我们拥有一些训练数据，在图中用圆点表示，我们希望找到一个函数能够较好地拟合这些数据。

    <figure>
        <img src="../assets/正则化例子2.png" width="70%">
    </figure>

    我们通过训练得到了两个函数 $f_1$ 和 $f_2$，其中 $f_1$ 没有引入正则化项，结果是一条曲线（高阶函数），$f_2$ 引入了正则化项，是一个简单的线性函数，显然 $f_1$ 在训练集上的表现要比 $f_2$ 好。

    <figure>
        <img src="../assets/正则化例子2.png" width="70%">
    </figure>

    但当我们把这两个函数应用到训练集之外的数据（unseen data）上时，会发现 $f_1$ 由于为了尽可能拟合训练集，导致在训练集之外的数据上表现很差，而 $f_2$ 由于引入了正则化项，使得它的泛化能力更强，因此整体表现反而更好。

    此外我们还可以发现，引入正则化项后，训练得到的模型通常会更加简单，这也有助于我们更好地理解模型的行为。

总而言之：**Loss function** consists of **data loss** to fit the training data and **regularization** to prevent overfitting

## Optimization

优化的目的是找到一个最佳的 $w$，使得损失函数 $L(w)$ 最小，既
$$ w^* = \arg \min\limits_w L(w) $$

<figure>
    <img src="../assets/优化-负梯度1.png" width="70%">
</figure>

我们知道当我们让 $w$ 沿着损失函数的负梯度方向移动时，损失函数下降得最快，这就是梯度下降法（gradient descent）。但这么做有一个问题：如果 $W$ 的维度很高（或者说元素数量很多），那么如果我们要对每一个元素都计算一次梯度，那么计算量就会非常大，同时计算得到的数值解也未必精确。

<figure>
    <img src="../assets/优化-负梯度2.png" width="70%">
</figure>

**Numeric Gradient**:

- Slow: O(#dimensions)
- Approximate

那么我们可以考虑使用**解析梯度**（analytic gradient），即通过求导的方式直接得到梯度的表达式。

- **Numeric gradient**: approximate, slow, easy to write
- **Analytic gradient**: exact, fast, error-prone

!!! note "Best pratice"
    Always use analytic gradient, but check implementation with numerical gradient. This is called a **gradient check**.

    在 Torch 中，我们可以使用 `torch.autograd.gradcheck` 来检查我们的梯度计算是否正确。

梯度下降法的主要思路就是不断迭代，每次迭代都让 $w$ 沿着损失函数的负梯度方向移动一小步，直到损失函数收敛。

```Python title="Vanilla Gradient Descent"
w = initialize_weights()
for t in range(num_steps):
    dw = compute_gradient(loss_fn, data, w)
    w -= learning_rate * dw
```

**Hyperparameters**:

- Weight initialization method
- Number of steps
- Learning rate

!!! advice
    在实践中，我们应该总是使用解析梯度来计算梯度，但是在实现之后，我们应该使用数值梯度来检查我们的实现是否正确是否正确，这就是所谓的**梯度检查**（gradient check）。

    在 PyTorch 中，我们可以使用 `torch.autograd.gradcheck` 和 `torch.autograd.gradgradcheck` 来分别检查一阶和二阶梯度。

    - 我们只需要验证一阶和二阶梯度是否正确，而不需要验证更高阶的梯度，因为一阶和二阶梯度的正确性可以保证更高阶梯度的正确性。

### Stochastic Gradient Descent(SGD)

$$ L(W) = \frac{1}{N} \sum^N_{i=1} L_i(x_i, y_i, W) + \lambda R(W) $$
$$ \nabla_W L(W) = \frac{1}{N} \sum^N_{i=1} \nabla_W L_i(x_i, y_i, W) + \lambda \nabla_W R(W) $$

在上面的梯度下降法中，我们每次迭代都要计算整个数据集的梯度，这在数据量 $N$ 很大的情况下是非常耗时的。

这时候我们可以考虑对这个数据集进行采样，每次只计算一个样本的梯度，这就是随机梯度下降法（Stochastic Gradient Descent，SGD）。

```Python title="Stochastic Gradient Descent"
w = initialize_weights()
for t in range(num_steps):
    minibatch = sample_data(data, batch_size)
    dw = compute_gradient(loss_fn, minibatch, w)
    w -= learning_rate * dw
```

这里涉及到的超参数有：

- Weight initialization
- Number of steps
- Learning rate
- Batch size

    采样时 batch 的大小，通常是 32、64、128 等。

- Data sampling

    采样的方式，通常是随机采样。

!!! tip
    - 就实际经验而言，batch_size 通常不是影响最终结果的关键超参数，可以选择内存承受范围内的尽可能大的值。
    - 在图像分割问题中，data sampling 也不是一个关键超参数，但在其他问题中可能会有所不同。

现在损失函数就可以表示为整个数据分布的期望：

$$ \begin{aligned}
L(W) &= \mathbb{E}_{(x,y)\sim p_{data}} [L(x, y, W)] + \lambda R(W) \\\\
    &\approx \frac{1}{N} \sum^N_{i=1} L_i(x_i, y_i, W) + \lambda R(W)
\end{aligned} $$

$$ \begin{aligned}
\nabla_W L(W) &= \nabla_W\mathbb{E}_{(x,y)\sim p_{data}} [ L(x, y, W)] + \lambda \nabla_W R(W) \\\\
    &\approx \sum^N_{i=1} \nabla_W L_W(x_i, y_i, W) + \lambda \nabla_W R(W)
\end{aligned} $$

但是 SGD 也有一些问题：如果损失函数在某个方向下降得很快，但在其他方向上下降得很慢，那么就会出现在快速下降的方向上“震荡”，而在慢速下降的方向上“缓慢前进”的情况。

<figure>
    <img src="../assets/SGD问题1.png" width="70%">
</figure>

同时，SGD 在梯度为零的位置会停止迭代，这就意味着我们可能会在局部最小值和鞍点处停止。

<figure>
    <img src="../assets/SGD问题2.png" width="70%">
</figure>

另外，因为我们的梯度来自于采样的数据，容易受到噪声的影响，这就意味着我们的梯度方向可能并不是最优的。

我们可以给 SGD 引入动量（momentum），在每一个新的时刻，我们根据上一个时刻的“速度”和当前时刻的梯度来更新速度，并利用这个速度来更新权重。

<figure>
    <img src="../assets/SGD+动量1.png" width="70%">
</figure>

<figure>
    <img src="../assets/SGD+动量2.png" width="550%">
</figure>

??? note "SGD + Momentum 的两种形式"

    <figure>
        <img src="../assets/SGD + Momentum 的两种形式.png" width="80%">
    </figure>

    我们可能会遇见两种形式的 SGD + Momentum，这两者实际上是等价的，它们的主要区别是
    
    - 速度沿着负梯度方向更新，并且通过相加来更新权重；还是速度沿着负梯度方向更新，并且通过相减来更新权重。
    - 学习率 $\alpha$ 是在更新速度时使用，还是在更新权重时使用。

### Nesterov Momentum

Nesterov Momentum 是对 Momentum 的一种变体，它在“移动”之前就计算了根据当前速度将要移动到的下一个位置的梯度，然后再根据这个梯度来更新移动的矢量。

<figure>
    <img src="../assets/Nesterov Momentum1.png" width="70%">
</figure>

换言之，Nesterov Momentum 会“提前”计算下一个位置的梯度，然后将这个梯度与当前速度结合，求出实际的更新方向。

<figure>
    <img src="../assets/Nesterov Momentum2.png" width="70%">
</figure>

### AdaGrad

AdaGrad 的思想是根据每一个参数累计的历史梯度来调整学习率，使学习率不断减小。

```Python title="AdaGrad"
grad_squared = 0
for t in range(num_steps):
    dw = compute_gradient(w)
    grad_squared += dw * dw
    w -= learning_rate * dw / (grad_squared.sqrt() + 1e-7)
```

- progress along “steep” directions is damped
- progress along “flat” directions is accelerated

RMSProp 是 AdaGrad 的一种改进，它引入了衰减率（decay rate）来控制历史梯度平方和的累积速度。

<figure>
    <img src="../assets/RMSProp.png" width="80%">
</figure>

### Adam

Adam 结合了 Momentum 和 RMSProp 的优点，利用梯度以及梯度的历史平方和来更新权重。

<figure>
    <img src="../assets/Adam.png" width="80%">
</figure>

同时我们还注意到，在开始时 t=1 时，由于 $\beta_2$ 的值通常是 0.999，这会使得 moment2 的值非常小，从而导致实际的学习率非常大，造成不稳定性。

于是 Adam 还引入了偏移校正（bias correction），用于处理在开始时刻 moment1 和 moment2 的值过小的问题。

!!! note
    Adam with $\beta_1 = 0.9, \beta_2 = 0.999$, and learning_rate = 1e-3, 5e-4, 1e-4 is a great starting point for many models! 

!!! abstract "Optimization Algorithm Comparison"

    <figure>
        <img src="../assets/优化算法比较.png" width="80%">
    </figure>

<figure>
    <img src="../assets/L2正则化项与权重衰减.png" width="70%">
</figure>

目前为止我们学习的优化算法都是对损失函数进行一阶求导的优化，即一阶优化算法。它们相当于利用梯度对损失函数进行线性近似，然后根据这个近似来更新权重。

<figure>
    <img src="../assets/一阶优化.png" width="70%">
</figure>

事实上我们还有二阶优化算法，例如牛顿法（Newton's method），它利用了损失函数的二阶导数信息，从而可以更好地近似损失函数。

<figure>
    <img src="../assets/二阶优化.png" width="70%">
</figure>

但是二阶优化的问题在于计算量非常大，因为它需要计算损失函数的 Hessian 矩阵，而这个矩阵的大小是 $O(N^2)$ 的，并且我们还需要计算它的逆矩阵，计算一个逆矩阵的时间复杂度是 $O(N^3)$。

In practice:
- **Adam** is a good default choice in many cases 
- **SGD+Momentum** can outperform Adam but may require more tuning
- If you can afford to do full batch updates then try out 
**L-BFGS** (and don't forget to disable all sources of noise)

!!! summary

    1. Use **Linear Models** for image classification problems
    2. Use **Loss Functions** to express preferences over different choices of weights
    3. Use **Regularization** to prevent overfitting to training data
    4. Use **Stochastic Gradient Descent** to minimize our loss functions and train the model
