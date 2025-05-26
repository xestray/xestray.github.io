---
    comments: true
    statistics: true
---

# 运动估计&图像分割

!!! info "运动估计"
    运动估计在从连续的图像序列中估计物体或相机的运动，通常用于视频分析、机器人导航和增强现实等应用。

## 运动估计

### 光流

> 可参考[这里的笔记](../selfstudy/EECS498/lec20.md#optical-flow)

光流法即通过比较连续的图像或视频帧中的像素变化来估计物体或相机的运动。它假设

- 像素的亮度保持不变
- 物体在连续帧之间的运动是平滑的
- 运动幅度较小

<figure markdown="span">
    ![](./assets/运动估计1.png){width=75%}
</figure>

在物体（像素）的运动距离较短时，我们可以使用泰勒级数展开来近似像素的亮度变化，即

$$ I(x+dx, y+dy, t+dt) \approx I(x, y, t) + \frac{\partial I}{\partial x} dx + \frac{\partial I}{\partial y} dy + \frac{\partial I}{\partial t} dt $$

由于我们假设相同对象的像素灰度值不变，就有 $I(x+dx, y+dy, t+dt) = I(x, y, t)$，因此小区相同项，并都除以 $dt$，我们可以得到

$$ I_x u + I_y v + I_t = 0 $$

其中 $I_x, I_y, I_t$ 分别是图像在 $x, y, t$ 方向的梯度，$u = dx/dt, v = dy/dt$ 分别是像素在 $x, y$ 方向的速度。这个式子被称为**光流约束方程**。

!!! note 
    对于单个像素，光流约束方程对应 $uv$ 坐标中的一条直线

    <figure markdown="span">
        ![](./assets/运动估计2.png){width=75%}
    </figure>

    而对于多个像素，我们会得到一簇直线，它们会存在公共交点（如果有不止一个公共角点，那么就取交于某点数量最多的一簇）

    <figure markdown="span">
        ![](./assets/运动估计3.png){width=75%}
    </figure>

### Lucas-Kanade 法

Lucas-Kanade 法是一种常用的光流估计方法，它假设在一个小的局部窗口内，光流是平滑的（窗孔内的点具有相同的运动）。因此我们就可以利用这些约束方程的方程组来求解未知数 $u, v$。

例如我们可以使用最小二乘法来求解 $u, v$，即最小化以下目标函数
$$ E(u, v) = \sum_{i=1}^{N} (I_x^i u + I_y^i v + I_t^i)^2 $$

<figure markdown="span">
    ![](./assets/运动估计4.png){width=75%}
    ![](./assets/运动估计5.png){width=75%}
</figure>

当出现以下情况时 Lucas-Kanade 法会失败：

- 像素亮度不保持一致
- 物体运动幅度较大
- 窗口内的点不具有相同的运动
    - 通常此时认为我们取的窗口太大了

有时为了保证光流法估计的准确性，还会进行分层的光流估计，即对图片进行下采样，然后在不同的分辨率下进行光流估计，最后将结果上采样到原始分辨率。

<figure markdown="span">
    ![](./assets/运动估计6.png){width=75%}
</figure>

## 图像分割

图像分割（Image Segmentation）是将图像分成多个有意义的区域或对象的过程，以便更容易地分析和理解图像内容。

- 不连续性：指区域之间像素值差别比较大，在边界上具有某种不连续性。
- 相似性：指区域内部像素值之间具备一定的相似性。

进行图像分割的方法包括聚类、阈值分割、边缘检测等。

!!! note "k-means 聚类"
    k-means 聚类是图像分割中常用的一种方法，它通过将像素分成 $k$ 个簇来实现分割，但是在这里就不罗嗦重复了。

    - 优点：简单快速，易于实现
    - 缺点：需要预先指定 $k$ 值，对噪声和异常点敏感，可能会陷入局部最优

    <figure markdown="span">
        ![](./assets/图像分割1.png){width=75%}
    </figure>

### mean-shift 分割

mean-shift 分割是一种基于密度的图像分割方法，它通过在特征空间中寻找高密度区域来实现分割。它的基本思想是通过迭代地移动数据点到其邻域内的平均值来找到数据的密集区域。

<figure markdown="span">
    ![](./assets/图像分割2.png){width=75%}
</figure>

算法的过程如下：

- 选择一个核函数和考虑的窗口大小（为简便起见，窗口不一定是圆，也可以是正方形或矩形）
- 对于每一个点
    - 将这个点作为窗口的中心
    - 计算窗口范围内所有点的平均值
    - 将窗口移动到新的平均值位置
    - 重复上述步骤，直到平均值不再变化（收敛）
- 将所有点分配到最近的平均值所在的簇中

常见的核函数有：

- 高斯核函数：
    $$ K(x) = \frac{1}{\sqrt{2\pi}} e^{-\frac{x^2}{2}} $$
- 均匀核函数：
    $$ K(x) = \begin{cases} 
    \frac{1}{2h} & \text{if } |x| < h \\
    0 & \text{otherwise}
    \end{cases} $$
- 三角核函数：
    $$ K(x) = \begin{cases} 
    \frac{1}{2h} (1 - \frac{|x|}{h}) & \text{if } |x| < h \\
    0 & \text{otherwise}
    \end{cases} $$
- Epanechnikov 核函数：
    $$ K(x) = \begin{cases} 
    \frac{3}{4h} (1 - \frac{x^2}{h^2}) & \text{if } |x| < h \\
    0 & \text{otherwise}
    \end{cases} $$

!!! note "mean-shift 分割的优缺点"
    - 优点：
        - 不需要预先指定簇的数量，能够处理任意形状的簇
        - 对噪声和异常点不敏感
    - 缺点：
        - 计算复杂度较高，可能会受到窗口大小的影响
        - 需要预先指定核函数
        - 不适用于高维数据

!!! example "mean-shift 分割效果示例"
    <figure markdown="span">
        ![](./assets/图像分割3.png){width=75%}
    </figure>

### graph cut 分割

graph cut 分割是一种基于图论的图像分割方法，它将图像看作一个图，节点表示像素，边表示像素之间的相似性。通过最小化图的割（cut）来实现分割。

在一个流网络中，最大流等于最小割的容量，我们可以利用这个定理来解决图像分割问题。

> 关于图的割和流的概念在这里不在重复了

!!! info "Ford-Fulkerson 算法"
    Ford-Fulkerson 算法是一种求解最大流问题的算法，它通过不断寻找增广路径来增加流量，直到没有增广路径为止。

    - 初始化：将所有边的流量设置为 0
    - 重复以下步骤，直到没有增广路径：
        - 在残量网络中寻找增广路径
        - 计算增广路径上的最小容量
        - 沿着增广路径增加流量
        - 更新残量网络

    <figure markdown="span">
        ![](./assets/图像分割4.png){width=65%}
    </figure>
