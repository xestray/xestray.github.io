---
    comments: true
    statistics: true
---

# Neural Networks

## Feature Transforms

在几何视角中我们可以把数据集看作是一个高维空间中的点集，线性分类器的目标是找到若干个个超平面，将不同类别的点分开。但很显然在很多情况下我们不能用简单的线性分类器来把非线性的数据分开。

解决这个问题的方法之一是使用特征变换，将数据从原始空间映射到一个新的空间，使得在新的空间中数据是线性可分的。

例如下面这个例子里我们把笛卡尔坐标系中的点映射到了极坐标系中，使得原本在笛卡尔坐标系中线性不可分的数据在极坐标系中变得线性可分。然后再把极坐标系中的点映射回笛卡尔坐标系中，这样就实现了对于原始数据的分类。

<figure>
    <img src="../assets/特征变换1.png" width="70%">
</figure>

对于图像特征，我们还有几种主要的特征变换方法：

- **Color Histograms**：将图像的颜色直方图作为特征，这样可以忽略图像的空间信息，只关注颜色信息。
- **Histogram of Oriented Gradients (HOG)**：将图像的梯度方向直方图作为特征，这样可以忽略图像的颜色信息，只关注梯度信息。某种意义上与 Color Histograms 对偶。
- **Bag of Words**：将图像的局部特征聚类，然后用聚类中心的频率直方图作为特征。

### Color Histograms

<figure>
    <img src="../assets/特征变换例1.png" width="70%">
</figure>

- 首先将图像利用某种色彩空间表示出来，例如 RGB 或 HSV。
- 讲色彩空间中的像素点分成若干个 bin，然后统计每个 bin 中的像素点数量。
- 对于图像中的每一个像素点，将其分到对应的 bin 中，然后统计每个 bin 中的像素点数量。
- 最后还可以对直方图进行归一化（标准化），从而允许不同尺寸的图像之间进行比较。

### Histogram of Oriented Gradients (HOG)

<figure>
    <img src="../assets/特征变换例2.png" width="70%">
</figure>

- 对于图像中的每一个像素点，计算其在水平和竖直方向上的梯度，并计算梯度的幅度和方向。
- 把图像划分为多个小单元（cell），在每个小单元内统计该区域的梯度方向分布。
- 对于每个小单元内的所有像素，HOG 会根据其梯度方向计算一个方向直方图，例如把梯度方向划分为多个 bin（例如 9 个方向，每个 bin 为 20 度），然后根据每个像素的梯度方向和幅值，更新相应方向 bin 的值。
- 接着 HOG 还会将相邻的几个单元（2x2 或 3x3）合并为一个 block，然后对 block 内的所有直方图进行归一化，这样可以减少光照变化、对比度变化的影响。
- 最后将所有 block 的直方图串联起来，作为图像的特征。

### Bag of Words

<figure>
    <img src="../assets/特征变换例3.png" width="70%">
</figure>

- 首先在图像中提取若干个局部特征点，例如 SIFT(Scale-Invariant Feature Transform) 或 SURF(Speeded-Up Robust Features)，将它们聚类为“visual words” 或 “codebook”。
- 对于图像的每个局部特征点，计算其与若干个聚类中心的距离，然后将其分配到距离最近的聚类中心。
- 统计每个聚类中心的频率（词频），然后将频率直方图作为图像的特征。

## Neural Networks

假设我们的输入是 $x \in \mathbb{R}^D$，希望得到的输出是 $y \in \mathbb{R}^C$，那么

- 在先前的线性分类器中，我们学习到的是一个线性映射 $f(x) = Wx + b$，其中 $W \in \mathbb{R}^{C \times D}$ 与 $b \in \mathbb{R}^C$ 是我们需要学习的参数。
- 而现在我们转而使用 2 层的神经网络，那么我们有

    $$ f(x) = W_2\ \sigma(W_1x + b_1) + b_2 $$

    其中 $W \in \mathbb{R}^{H \times D},\ b_1 \in \mathbb{R}^H,\ W_2 \in \mathbb{R}^{C \times H},\ b_2 \in \mathbb{R}^C$ 是我们需要学习的参数，$\sigma$ 是激活函数。

    如果是 3 层的神经网络，那么就是

    $$ f(x) = W_3\ \sigma(W_2\ \sigma(Wx + b_1) + b_2) + b_3 $$

这里我们使用 ReLU 作为激活函数，ReLU 的定义是 $\sigma(x) = \max(0, x)$。

<figure>
    <img src="../assets/神经网络结构.png" width="70%">
</figure>

这里前一层的每一个神经元都与后一层的每一个神经元相连，称为全连接神经网络（Fully Connected Neural Network），有时也称为多层感知机（Multi-Layer Perceptron, MLP）。

!!! info "Deep Neural Networks"
    深度神经网络是指神经网络中有多个隐藏层的神经网络，这让它可以学习到更复杂的数据特征。

    深度神经网络的深度是指神经网络中的层数（实际上是权重矩阵的数量），而宽度是指每一层神经元的数量。

    一般而言，当我们说 N 层神经网络时，不包括输入层，因此单层神经网络描述的是没有隐藏层的网络（输入直接映射到输出），而 2 层神经网络描述的是有一个隐藏层的网络。

    <figure>
        <img src="../assets/深度神经网络.png" width="70%">
    </figure>

!!! example "衡量神经网络的大小"

    人们通常使用两个指标来衡量神经网络的大小，一个是神经元的数量，另一个是参数的数量。

    <figure>
        <img src="../assets/神经网络分层.png" width="70%">
    </figure>

    - 在左边这个网络中，有 4 + 2 = 6 个神经元，有 3 * 4 + 4 * 2 = 20 个参数，4 + 2 = 6 个 bias，共计 26 个可学习参数。
    - 在右边这个网络中，有 4 + 4 + 1 = 9 个神经元，有 3 * 4 + 4 * 4 + 4 * 1 = 32 个参数，4 + 4 + 1 = 9 个 bias，共计 41 个可学习参数。

### Activation Functions

<figure>
    <img src="../assets/激活函数.png" width="70%">
</figure>

如果没有激活函数，那么数据经过神经网络得到的结果仍然是输入的线性组合，仍旧无法处理复杂问题。例如
$$ f(x) = W_2(W_1x + b_1) + b_2 = (W_2W_1)x + (W_2b_1 + b_2) $$

但有了激活函数之后，我们就可以对数据进行一些处理，实现空间扭曲（space warping），从而使得数据在新的空间中更易于分离。

!!! example "Space Warping"
    <figure>
        <img src="../assets/空间扭曲1.png" width="70%">
        <img src="../assets/空间扭曲2.png" width="70%">
    </figure>

    假如仅仅进行线性的特征转换，那么在原始空间中线性不可分的数据在新的空间中仍然是线性不可分的。但如果我们使用非线性的激活函数，那么我们就可以实现空间扭曲，使得原本线性不可分的数据在新的空间中变得线性可分。

    但当我们应用了 ReLU 作为激活函数之后，原先 B 象限上的点就会被压缩到 h2 的正半轴上，原先 D 象限上的点就会被压缩到 h1 的正半轴上，而原先 C 象限上的点就会被压缩到原点上。

    <figure>
        <img src="../assets/空间扭曲3.png" width="70%">
        <img src="../assets/空间扭曲4.png" width="70%">
    </figure>

    这样一来在新的空间中，原本线性不可分的数据就变得线性可分了。
    
使用激活函数就相当于在原始空间中将超平面（Hyperplane）进行了折叠，使用的神经元和隐藏层越多，折叠的次数就越多，这样就可以实现更复杂的空间扭曲。

<figure>
    <img src="../assets/空间折叠1.png" width="65%">
</figure>

当然使用了过多的隐藏层和神经元也会导致模型过于复杂，这时我们可以使用正则化项来使分类的超平面更加平滑。

<figure>
    <img src="../assets/空间折叠2.png" width="65%">
</figure>

> The takeaway is that you should not be using smaller networks because you are afraid of overfitting. Instead, you should use as big of a neural network as your computational budget allows, and use other regularization techniques to control overfitting.

### Universal Approximation

具有一个隐藏层的神经网络可以以任意精度逼近任意连续函数 $f: \mathbb{R} \rightarrow \mathbb{R}$，这就是神经网络的万能逼近定理（Universal Approximation Theorem）。

<figure>
    <img src="../assets/神经网络万能逼近.png" width="80%">
</figure>

ReLU 的函数图形如上图所示，它是分段线性的，我们可以用 4 个 ReLU 函数的线性组合来构造一个突起的函数（bump function）。

<figure>
    <img src="../assets/突起函数1.png" width="50%">
</figure>

有了若干个这样的突起函数之后，我们就可以用足够数量的 bump function 来逼近任意函数。

<figure>
    <img src="../assets/突起函数2.png" width="50%">
</figure>

!!! note 
    这仅仅是在数学上说明了神经网络确实可以逼近任意函数，并没有告诉我们到底需要多少个神经元或隐藏层才能逼近目标函数，没有告诉我们该如何训练神经网络才能构造出这样的形式。
    
    在实际的应用中，经过训练后的神经网络并不会使用这样的凹凸函数来逼近目标函数。例如我们让神经网络拟合正弦函数，它的实际表现可能是这样的：

    <figure>
        <img src="../assets/拟合正弦函数.png" width="60%">
    </figure>

## Convex Functions

A function $f: x \in \mathbb{R}^N \rightarrow \mathbb{R}$ is convex if for any $x_1, x_2 \in X, t \in [0, 1]$ we have
$$ f(tx_1 + (1-t)x_2) \leq tf(x_1) + (1-t)f(x_2) $$

从高维的角度来看，凸函数的图像就类似于一个碗型

<figure>
    <img src="../assets/凸函数图像.png" width="50%">
</figure>

> Generally speaking, convexfunctions are **easy to optimize**: can derive theoretical guarantees about **converging to global minimum**

我们对线性分类器的优化问题是一个凸优化问题，因为线性分类器的损失函数是凸函数 

<figure>
    <img src="../assets/线性分类器优化.png" width="75%">
</figure>

但遗憾的是，大多数的神经网络的优化问题并不是凸优化问题

- Few or no guarantees about convergence
- Empirically it seems to work anyway
- Active area of research

> 那么最后的问题是：我们该如何计算梯度？我们可以在下一节 [Backpropagation](lec5.md) 中讨论这个问题。
