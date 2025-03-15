---
    comments: true
    statistics: true
---

# Linear Classification

## 三种视角

### 代数视角

<figure>
    <img src="../assets/线性分类器.png" width="70%">
</figure>

例如我们希望把一些 32x32x3 的图片分类为 10 个类别，那么我们可以计算每张图片在这 10 个类别上的评估分数，例如我们可以把图片展开为一个 3072 维的向量，然后用一个 10x3072 的矩阵 $W$ 与它相乘，就可以得到一个 10 维的向量，这个向量的每一个元素都是这张图片在对应类别上的评估分数。

我们还可以给相乘后的结果加上一个偏置项 $b$，$b$ 也是一个 10 维的向量，需要我们通过训练得到。

!!! example
    <figure>
        <img src="../assets/线性分类代数视角1.png" width="70%">
    </figure>    

    假设现在我们有一些 2x2 的图像，要把这些图像分为 cat/dog/ship 三类，就可以用上图中的方法来形象化的表达矩阵相乘的过程。

    我们可以注意到矩阵 $W$ 的每一行都对应着一个类别，而每一列都对应着一个像素。

    <figure>
        <img src="../assets/线性分类代数视角1.png" width="70%">
    </figure> 

    事实上我们也可以不把偏移量 $b$ 看作是一个单独的向量来训练，我们可以把它合并到矩阵 $W$ 中，并且在图像向量的末尾添加一个常数 1，于是我们就把 3x4 与 4x1 的矩阵相乘变为了 3x5 与 5x1 的矩阵相乘。

    > 从形象的角度来看，就相当于我们把偏移量向量左移合并到了权重矩阵中，再给每个图像向量的末尾添加一个常数 1。

    虽然这个技巧很有趣，但在实际应用中并不常用，因为当我们从线性分类器转向卷积时，它并不能很好的推广，并且有时我们也希望对于权重矩阵和偏移量矩阵有不同操作（正则化等）。 

### 视觉视角

<figure>
    <img src="../assets/线性分类器视觉视角1.png" width="70%">
</figure>

我们还可以转变一下思维，从“视觉视角”来考虑线性分类器。我们已经知道权重矩阵的每一行都对应一个类别，那么我们可以把每一行单独提取出来，重新组合成与图像大小相同的矩阵，现在当我们要计算各个类别的评估分数时，这两个矩阵对应位置的元素相乘再相加。

<figure>
    <img src="../assets/线性分类器视觉视角2.png" width="70%">
</figure>

现在我们就为每一种类型都创建了一个“模板”，当我们要计算某个图像在某个类别上的评估分数时，我们就可以把这个图像与这个类别的模板进行比较，图像和模板之间越相似，得分就越高（利用基础的线性代数知识我们可以知道，当两个向量的夹角（差别）越小，它们的点积就越大）

### 几何视角

<figure>
    <img src="../assets/线性分类器几何视角1.png" width="70%">
</figure>

最后我们还有一种几何视角。现在我们仅考虑图像中的一个像素，由于线性分类器是“线性”的，当这个像素的值变化时，各个种类对应的分数也会按照某种线性关系变化。

<figure>
    <img src="../assets/线性分类器几何视角2.png" width="70%">
</figure>

接下来我们就考虑多个像素的共同作用。考虑两个像素时，我们可以创造一个二维的坐标系，图中实线表示在这条线上的得分相同，虚线方向表示得分增加的方向。

当考虑多个像素时，这就变成了一个高维空间上的若干个超平面，每个超平面都对应着一个类别，而这些超平面的交点就是我们的分类边界。

## 损失函数

A **loss function** tells how good our current classifier is.

- Low loss = good classifier
- High loss = bad classifier

(Also called: objective function; cost function)

- Negative loss function sometimes called reward function, profit function, utility function, fitness function, etc

    与损失函数对应的是奖励函数（也可称为收益函数、效用函数等其他名称）。

例如我们考虑一个数据集
$$ \{ (x_i, y_i) \}^N_{i=1} $$
其中 $x_i$ 是输入（图像），$y_i$ 是标签（数字），$N$ 是数据集的大小。

那么一个简单的损失函数在图像 $x_i$ 上的值就可以写成
$$ L_i(f(x_i, W), y_i) $$
这个数据集上的平均损失就是
$$ L = \frac{1}{N} \sum_{i} L_i(f(x_i, W), y_i) $$

### Multiclass SVM Loss

现在我们来考虑一个多类别的线性分类器，我们使用的损失函数是多类别支持向量机（Multiclass SVM Loss）。这个损失函数的图像如下图所示，它的损失被称为“hinge loss”。

<figure>
    <img src="../assets/SVM损失函数.png" width="70%">
</figure>

这个函数对于损失的定义是：

- 对于每一个错误类别所获得的分数，如果正确类别的分数比错误类别的分数高出至少一个固定的边界值（margin，在这里被设定为1），那么损失为 0
- 否则损失为两者之差再加上 margin。

??? example
    <figure>
        <img src="../assets/SVM损失函数例1.png" width="70%">
    </figure>

!!! question
    1. What happens to the loss if the scores for the car image change a bit?

        当我们对这些分数进行很小的改变时（假设是一个充分小的 $\epsilon$），如果此时损失函数已经下降到 0，那么这个改变就不会对损失函数产生影响。

    2. What are the min and max possible loss?

        最小损失为 0，最大损失为无穷大。由于 SVM 损失函数的定义，因此损失值是可以达到 0 的。

    3. If all the scores were random, what loss would we expect?

        如果所有的评估分数都是随机的小值（例如可以考虑为一个方差非常小的高斯分布），那么损失值会接近 $C-1$，其中 $C$ 是类别的数量。

### Cross-entropy Loss

交叉熵损失函数（Cross-entropy Loss）是另一种常用的损失函数。

<figure>
    <img src="../assets/交叉熵损失函数.png" width="70%">
</figure>

这个例子中，我们首先通过 softmax 函数将评估分数转换为概率，然后求出正确类别的概率所对应的负对数，将其作为损失值。

我们很容易可以知道，当正确类别的概率越接近 1 时，损失值越接近 0。

我们把它称为交叉熵函数是因为它与两个概率分布之间的交叉熵具有单调性（涉及到一些没学过的信息论知识...）

!!! note "相对熵、交叉熵"
    相对熵又称为 KL 散度（Kullback–Leibler divergence），用来描述两个概率分布的差异性。假设有对同一变量 $x$ 有 $q(x)$ 和 $p(x)$ 两个概率分布，那么两者之间的相对熵可由以下定义：
    $$ D_{KL}(p||q) = \sum_x p(x) \log \frac{p(x)}{q(x)} $$

    交叉熵是相对熵的一个特例，当 $p$ 是真实分布，$q$ 是模型分布（预测出的概率分布）时，交叉熵可以表示为：
    $$ H(p, q) = H(p) + D_{KL}(p||q) $$
    或
    $$ H(p, q) = - \sum_x p(x) \log q(x) $$
    
总而言之，交叉熵损失函数可以把损失表示为
$$ L_i = - \log (\frac{e^{s_{y_i}}}{\sum_j e^{s_j}} )$$

当我们试图最小化交叉熵损失函数时，实际上就是希望模型的预测概率分布尽可能集中在正确的类别上（在这个分类问题中，正确类别的真实分布概率为 1，其余类别为 0）。

!!! question
    1. What is the min / max possible loss $L_i$?

        最小损失为 0，最大损失为无穷大。

    2. If all scores are small random values, what is the loss?

        如果所有的评估分数都是随机的小值（此时可以认为所有的类别都是等概率的），那么损失值会接近 $-\log(\frac{1}{C})$，其中 $C$ 是类别的数量。

        例如在 CIFAR10 数据集上，$C=10$，那么损失值会是 $-\log(\frac{1}{10}) \approx 2.3$
