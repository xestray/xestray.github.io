---
    comments: true
    statistics: true
---

# 波动光学

!!! abstract 
    在几何光学中，$\lambda << d$，即光波的波长远小于光程差，可以忽略波动性。而在波动光学中，$\lambda \approx d$，即光波的波长与光程差比较接近，波动性不能忽略。

    主要包括三个部分

    - Interference 干涉
    - Diffraction 衍射
    - Polarization 偏振

## 干涉

### 定态光波

所谓定态波就是指波的振幅和相位都与时间无关的波，例如
$$ U(P,t) = A(P) \cos(\omega t - \varphi(P)) $$
中的 $A(P)$ 和$ \varphi(P)$ 都仅仅是与空间有关的函数，而与时间无关。

!!! example
    - 定态平面波

        $$  \begin{aligned}
        & \begin{cases} A(P) = A_0 ,\text{constant, independent of (x,y,z)} \\ 
        \varphi(P) = \overrightarrow{k} \cdot \overrightarrow{r} + \varphi_0 = k_x x + k_y y + k_z z + \varphi_0 
        \end{cases} \\\\
        & k = \dfrac{2\pi}{\lambda}, \overrightarrow{r} = x \overrightarrow{i} + y \overrightarrow{j} + z \overrightarrow{k}
        \end{aligned} $$  


    - 定态球面波

        $$ \begin{cases} 
        A(P) = \dfrac{a}{r} \\ 
        \varphi(P) = kr \varphi_0 
        \end{cases} $$

光也是一种波，因此定态光波可以描述为
$$ \overrightarrow{E}(P,t) = \overrightarrow{E}_0 (P) \cos(\omega t - \varphi(P)) $$
$$ \overrightarrow{H}(P,t) = \overrightarrow{H}_0 (P) \cos(\omega t - \varphi(P)) $$

### 波的负数描述

$$ \begin{aligned}
& U(P,t) = A(P) \cos[\omega t - \varphi(P)] \\\\
\Longleftrightarrow \quad & \widetilde{U}(P,t) = A(P) e^{\pm i[\omega t - \varphi(P)]}
\end{aligned} $$

上面的正负符号的不同是由于欧拉公式的不同形式，但是在实际应用中，我们通常使用负号。

对于一个定态波，我们可以把它的空间和时间分离，即
$$ \widetilde{U}(P,t) = A(P) e^{i[\varphi(P) - \omega t]} = A(P) e^{i\varphi(P)} e^{-i\omega t} $$
其中，$\widetilde{U}(P) = A(P) e^{i\varphi(P)}$ 是波的复振幅，$e^{-i\omega t}$ 是波的时间因子。

!!! note
    - 平面波 $$ \widetilde{U}(P) =  e^{i(\overrightarrow{k} \cdot \overrightarrow{r} + \varphi_0)} = A e^{i(k_x x + k_y y + k_z z + \varphi_0)} $$
    - 球面波 $$ \widetilde{U}(P) = \dfrac{a}{r} e^{i(kr + \varphi_0)} $$

    波的强度与振幅的平方成正比，即 $I \propto |A|^2$，但是一般而言我们会直接使用等号
    $$ I(P) - [A(P)]^2 = \widetilde{U}^*(P) \cdot \widetilde{U}(P) $$

### 波的干涉与相干条件

两列波叠加的结果为
$$ \widetilde{U}(P,t) = \widetilde{U}_1(P,t) + \widetilde{U}_2(P,t) = [A_1(P) e^{i\varphi_1(P)} + A_2(P) e^{i\varphi_2(P)}] e^{-i\omega t} $$ 

因此波的强度为

$$ \begin{aligned}
I(P) &= \widetilde{U}^*(P) \cdot \widetilde{U}(P) \\\\
&= [A_1 e^{-i\varphi_1(P)} + A_2 e^{-i\varphi_2(P)}] [A_1 e^{i\varphi_1(P)} + A_2 e^{i\varphi_2(P)}] \\\\
&= A_1^2 + A_2^2 + A_1 A_2 [e^{i(\varphi_1 - \varphi_2)} + e^{-i(\varphi_1 - \varphi_2)}] \\\\
&= A_1^2 + A_2^2 + 2 A_1 A_2 \cos(\varphi_1 - \varphi_2)
\end{aligned} $$

由于 $I_1(P) = A_1^2, I_2(P) = A_2^2$，因此两列波的强度之和为
$$ I(P) = I_1(P) + I_2(P) + 2 \sqrt{I_1(P) I_2(P)} \cos(\varphi_1 - \varphi_2) $$

!!! note "干涉现象的光强分布"
    <figure>
        <img src="../assets/干涉现象的光强分布.png" width="70%">
    </figure>

    因此一般来说 $I(P) \neq I_1(P) + I_2(P)$，只有当 $\varphi_1 - \varphi_2 = 2n\pi$ 时，两列波才是完全相干的。

    $$ \begin{cases}
    \cos(\varphi_1 - \varphi_2) > 0, \quad I(P) > I_1(P) + I_2(P) \\\\
    \cos(\varphi_1 - \varphi_2) < 0, \quad I(P) < I_1(P) + I_2(P)
    \end{cases} $$

    两个光波之间的相位差 $\Delta \varphi = \varphi_1 - \varphi_2$ 是干涉现象的关键，当相位差不固定时，那么它在在统计意义上的平均值为零，即
    $$ \overline{\cos \Delta \varphi} = \overline{\cos(\varphi_1 - \varphi_2)} = 0 $$
    相位差会随时间变化时，我们称两列波不相干（incoherent）。

除了相位差需要保持固定之外，两个波的频率还需要保持一致，即 $\omega_1 = \omega_2$，这就是两个波的相干条件。

### 介质中的光程差

相位差在分析光的干涉时十分重要，为便于计算光通过不同媒质时的相位差，我们引入“光程差”的概念。

根据已经学习到的知识，我们知道光程就是 $L = \int n ds$，因此光程差就是。由于光波的相位与它传播的距离，即光程相关，那么我们可以知道相位差与光程差的关系为
$$ \Delta \varphi = \dfrac{2\pi}{\lambda} \Delta L $$
其中 $\Delta L$ 是光程差，$\lambda$ 是光的波长


!!! summary "光的干涉" 
    光相干的条件为

    - 频率相同
    - 振动方向相同
    - 相位差恒定

    判断光是否干涉：
    $$ \begin{aligned}
    & \Delta \varphi = 
    \begin{cases} 
    \pm 2k\pi, &k=0,1,2,\cdots \text{干涉增强} \\\\
    \pm (2k+1)\pi, &k=0,1,2,\cdots \text{干涉减弱}
    \end{cases} \\\\
    & \Delta L =
    \begin{cases}
    \pm k\lambda, &k=0,1,2,\cdots \text{干涉增强}\\\\
    \pm (2k+1)\dfrac{\lambda}{2}, &k=0,1,2,\cdots \text{干涉减弱}
    \end{cases}
    \end{aligned} $$

### 杨氏双缝干涉实验

<figure>
    <img src="../assets/杨氏双缝干涉实验.png" width="65%">
</figure>

杨氏双缝实验需要满足的条件是

- 两缝之间的间距 $d$ 要远大于光的波长 $\lambda$，即 $d >> \lambda$
- 缝到屏幕的距离 $D$ 要远大于缝之间的距离 $d$，即 $D >> d$。

<figure>
    <img src="../assets/杨氏双缝干涉实验1.png" width="65%">
</figure>

光程差 $\Delta L = r_2 - r_1 \approx d \sin \theta \approx d \tan\theta = d \cdot \dfrac{y}{D}$

- 干涉相长：$d \sin\theta = m\lambda$
- 亮纹中心：$x = \dfrac{m\lambda D}{d}$
- 干涉相消：$d \sin\theta = (m + \dfrac{1}{2})\lambda$
- 暗纹中心：$x = \dfrac{(m + \dfrac{1}{2})\lambda D}{d}$
- 两相邻亮纹（暗纹）的间距：$\Delta x = \dfrac{D}{d}\lambda$

!!! question
    - 将双缝干涉装置由空气中放入水中时，屏上的干涉条纹有何变化？

        由于水的折射率大于空气折射率，因此光在水中的波长减小，根据 $\Delta x = \dfrac{D}{d}\lambda$，条纹间距会减小

    - 若使用白光为光源进行双缝干涉实验，可以观察到几级清晰可辨的彩色光谱？

        用白光照射时，在中央白色明纹两侧，只有第一级彩色光谱是清晰可辨的。

!!! tip
    当两缝的间距小于光的波长时，将无法看到干涉亮条纹。

    因为亮条纹要求 $d \sin\theta = m \lambda$，即 $\sin\theta = \dfrac{m \lambda}{d}$。
    
    当 $d < \lambda$ 时，就有 $\sin\theta > 1$，这是不可能的，因此无法看到亮条纹。

### 洛埃德镜实验

<figure>
    <img src="../assets/洛埃德镜实验.png" width="80%">
</figure>

- 当屏移到 $A'B'$ 位置时，在屏上的 $P$ 点会出现暗条纹。这一实验结果表明当光在镜子表面发生反射时，反射光的相位会变化一个 $\pi$。

!!! note "半波损失（Reflection Phase Shifts）"
    - 光从光疏介质反射到光密介质，反射光的相位会变化一个 $\pi$。
    - 光从光密介质反射到光疏介质，反射光的相位不变。

    <figure>
        <img src="../assets/半波损失.png" width="80%">
    </figure>

    可以类比于更一般的波的反射，当波传播到更“难”传播，或者说更“稠密”的介质中时，反射回来的波会有一个 $\pi$ 的相位差，这就是半波损失。

    - **折射波不会出现半波损失**

### 等厚干涉

![](./assets/等厚干涉.png){align=right width=24%}

可以通过计算证明，在分别在上表面和下表面反射的两束光的光程差为
$$ \Delta L = 2nh \cos i $$

- 亮条纹（maxmum）：$\Delta L = 2nh \cos i = m\lambda$
- 暗条纹（minmum）：$\Delta L = 2nh \cos i = (m + \dfrac{1}{2})\lambda$

!!! tip
    这里要注意，如果 $n_1 < n$，那么在反射时会产生半波损失，那么光程差就相当于 $2nh \cos i + \dfrac{\lambda}{2}$。

通常而言我们考虑垂直入射的情况，即 $i = 0$，那么光程差就化简为
$$ \Delta L = 2nh $$

#### 劈尖膜

<figure>
    <img src="../assets/劈尖膜.png" width="70%">
</figure>

由于光线在空气劈尖的下表面发生反射时会有半波损失，因此实际的光程差为
$$ \Delta L = 2nh + \dfrac{\lambda}{2} $$
其中 $h$ 为入射位置对应的空气劈尖厚度。

于是发生干涉时，亮条纹和暗条纹的条件为

$$ \Delta L = 2nh + \dfrac{\lambda}{2} = \begin{cases}
m\lambda, & m = 0,1,2,\cdots \text{亮条纹} \\
(m + \dfrac{1}{2})\lambda, & m = 0,1,2,\cdots \text{暗条纹}
\end{cases} $$

亮、暗条纹对应的厚度为

$$ h = \begin{cases}
\dfrac{(m-\dfrac{1}{2})}{2n} \lambda, & m = 1,2,3,\cdots \quad \text{亮条纹} \\\\
\dfrac{m}{2n} \lambda, & m = 0,1,2,\cdots \quad \text{暗条纹}
\end{cases} $$

于是相邻的两个条纹对应的厚度差为 $\Delta h = \dfrac{\lambda}{2n}$

!!! example 
    <figure>
        <img src="../assets/劈尖例题.png" width="70%">
    </figure>

    如果工件的表面是完全平滑没有凹凸的，那么等厚干涉条纹将会是等间距交错的明暗条纹，并且所有连续条纹对应位置的空气劈尖厚度一定是相同的。

    如果出现下凹，那么下凹处的披肩厚度将会比周围的厚度大，因此会观测到干涉条纹向着厚度更低的地方弯曲。

    例如上图中干涉条纹弯向空气膜的左端，红线上的劈尖厚度处处相等，因此可以知道左侧的表面出现了下凹。利用相似三角形的知识我们可以知道
    $$ \dfrac{a}{b} = \dfrac{\Delta h}{e_k - e_{k+1}} = \dfrac{\Delta h}{\lambda / 2} $$
    这里 $e_k$ 表示第 $k$ 级条纹对应的劈尖厚度，并且在空气中 $n=1$，所以
    $$ \Delta h = \dfrac{a}{b} \dfrac{\lambda}{2} $$

#### 牛顿环

![](./assets/牛顿环.png){align=right width=30%}

$$ \Delta L = 2e + \dfrac{\lambda}{2} = \begin{cases}
m\lambda, & m = 0,1,2,\cdots \text{亮纹} \\
(m + \dfrac{1}{2})\lambda, & m = 0,1,2,\cdots \text{暗纹}
\end{cases} $$

根据几何关系，
$$ r^2 + (R - e)^2 = R^2 $$
由于 $e$ 很小，所以它的二次项可以忽略，因此
$$ e = \dfrac{r^2}{2R} $$

导入到上面光程差的式子中，计算得到

$$ r = \begin{cases}
\sqrt{(m - \dfrac{1}{2})\lambda R}, & m = 1,2,3,\cdots \quad \text{亮条纹} \\\\
\sqrt{m \lambda R}, & m = 0,1,2,\cdots \quad \text{暗条纹}
\end{cases} $$

于是我们知道牛顿环干涉条纹是一系列明暗相间的同心圆环，并且中心是一个暗斑。

![](./assets/牛顿环1.png){align=right width=20%}

由于
$$ \Delta r = r_{m+1} - r_m = \dfrac{\sqrt{R\lambda}}{\sqrt{R} + \sqrt{R+1}} $$
因此牛顿环的条纹间距是不等的，且内疏外密。









## 衍射







































## 偏振