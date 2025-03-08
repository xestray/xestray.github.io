---
    comments: true
    statistics: true
---

# 流水线

<figure>
    <img src="../assets/流水线概括.png" width="70%">
</figure>

## What is pipelining?

有两种方式加速程序的执行

- 缩短每条指令的执行时间
    - 例如使用高速设备、提升指令内部微操作的并行度
- 对一段程序整体的执行进行加速
    - 使用流水线、多发射等方式

比如一条指令可以被分为 3 个阶段

<figure>
    <img src="../assets/三段指令.png" width="55%">
</figure>

那么我们就有下面三种执行模式：

- Sequential execution
- Single overlapping execution
- Twice overlapping execution

### Sequential execution

<figure>
    <img src="../assets/三段指令.png" width="55%">
</figure>

即按顺序依次执行每一条指令，执行时间等于每条指令的执行时间乘以指令的数量

### Overlapping execution

重叠执行时，如果不同阶段的时间不一致，那么就可能导致等待，造成资源的浪费；如果不等待，那么用时较长的阶段就可能因同时使用同一份硬件资源而导致冲突。

<figure>
    <img src="../assets/重叠执行1.png" width="50%">
</figure>

<figure>
    <img src="../assets/重叠执行2.png" width="50%">
</figure>

最理想的情况是每个阶段的时间都相等，就有 single 和 twice overlapping execution 两种方案，即同时有额外的一个或两个指令在执行。

<figure>
    <img src="../assets/single.png" width="50%">
</figure>

<figure>
    <img src="../assets/double.png" width="50%">
</figure>

流水线可能产生内存访问冲突，常见的解决方案有

- **Instruction memory** & **data memory**
- **Instruction cache** & **data cache** (same memory): Harvard structure
- **Multibody cross structure** (same memory with limitations)
- Adding **instruction buffer** between memory and instruction decode unit

<figure>
    <img src="../assets/指令缓冲区.png" width="65%">
</figure>

添加了 buffer 之后，可以在 buffer 满之前一直不停把指令存放到里面，这样取指令的时间就变得很短，此时 IF 就可以和 ID 阶段合并。

但这样合并过后的 ID 阶段的时间也未必与 EX 阶段一致，那么我们可以再给 ID 阶段之后添加一个 buffer，把 decode 的结果存放到这个 buffer 里面，这样下一次的 ID 就不必等到当前的 EX 执行结束才开始。

<figure>
    <img src="../assets/取指后buffer.png" width="55%">
</figure>

<figure>
    <img src="../assets/取指后buffer时间.png" width="60%">
</figure>

## Classes of pipelining

- **Single function pipelining（单功能流水线）**: only one fixed function pipelining.
-  **Multi function pipelining（多功能流水线）**: each section of the pipelining can be
connected differently for several different functions.

    - 执行不同的运算（功能）时会使用流水线中不同的段

??? example "多功能流水线"
    <figure>
        <img src="../assets/多功能流水线.png" width="60%">
    </figure>

针对于多功能流水线，还可以继续细分为两种：

- Static pipelining

    静态流水线：同一个时刻流水线内只能做某个特定的功能。

    需要执行不同的功能时，需要等待当前的所有功能执行完毕后才可以切换。

- Dynamic pipelining

    动态流水线：同一个时刻流水线可以做多个功能。

    执行指令更灵活，但控制起来也更复杂。

!!! example "静态与动态流水线"
    <figure>
        <img src="../assets/静态与动态流水线.png" width="65%">
    </figure>

    横轴表示时间，纵轴表示此时使用到的是流水线的那一部分硬件（空间资源）。

流水线也可以按照划分粒度的不同进行分类

- Component level pipelining (in component - operation pipelining)

    处理器内部的算术和逻辑操作组件被分为多个段，实现部件级别的流水线

- Processor level pipelining (inter component - instruction pipelining)

    指令级流水线，将指令分为多个子阶段，每个子阶段由不同功能单元执行

- Inter processor pipelining (inter processor - macro pipelining)

    多处理器之间的流水线，将多个处理器连接起来，每个处理器完成一个大任务的一部分

也可以分为线性/非线性流水线

- Linear pipelining
- Nonlinear pipelining

    指令的执行过程可能有回路，某些操作单元可能会被多次使用

<figure>
    <img src="../assets/非线性流水线.png" width="65%">
</figure>

还有其他分类方法：

- 顺序与乱序流水线

    - Ordered pipelining（顺序流水线）：指令的执行顺序与指令的发射顺序一致
    - Disordered pipelining（乱序流水线）：指令的执行顺序与指令的发射顺序不一致

- 标量/向量处理器

    - Scalar pipelining（标量流水线）
    - Vector pipelining（向量流水线）：
    
    The processor has vector data representation and vector instructions. It is the combination of vector data representation and pipelining technology.

## Performance evaluation of pipelining

### Throughput

流水线的目的是提高单位时间内执行的指令数目，即提高吞吐率（throughput）。
$$ TP = \frac{N}{T_K} < TP_{max} $$

<figure>
    <img src="../assets/吞吐率.png" width="70%">
</figure>

$$ TP=\dfrac{n}{n+m-1}TP_{max} $$

当流水线各个阶段的耗时不等时，最大吞吐率 $TP_{max}$ 取决于最慢的阶段（bottleneck segment）。

???+ example 

    - $M = 4$
    - Time of S1, S3, S4: $\delta t$
    - Time of S2: $3\delta t$ (Bottleneck)

    <figure>
        <img src="../assets/瓶颈段1.png" width="70%">
    </figure>

    <figure>
        <img src="../assets/瓶颈段2.png" width="70%">
    </figure>

    $TP_{max}$ 仅和瓶颈段的时间有关

解决瓶颈问题的方法有两种

- Subdivision

    把瓶颈段分成若干段执行

    <figure>
        <img src="../assets/解决瓶颈段1.png" width="80%">
    </figure>

- Repetition

    对多条指令的瓶颈段进行重复执行（并行执行），对单条指令而言没有提升，但对多条指令可以提升吞吐率

    <figure>
        <img src="../assets/解决瓶颈段2.png" width="70%">
    </figure>

    <figure>
        <img src="../assets/解决瓶颈段3.png" width="60%">
    </figure>

### Speedup

加速比（speedup）是指在流水线中总的执行时间相较于不使用流水线的提升程度。
$$ Sp = \dfrac{T_{seq}}{T_{pipe}} $$

$$ S_p = \dfrac{n\times m \times \Delta t_0}{m(m+n-1)\Delta t_0} = \dfrac{n}{m+n-1} $$

- if $n>>m, Sp \approx m$

### Efficiency

我们这里定义的效率（efficiency，$\eta$）是指对于计算机时空资源的利用程度，对于下面的时空图而言，效率就等于实际使用计算机资源的格子数除以总的格子数。

<figure>
    <img src="../assets/效率.png" width="70%">
</figure>

$$ \eta = \dfrac{n\times m \times \Delta t_0}{m(m+n-1)\Delta t_0} = \dfrac{n}{m+n-1} $$

- if $n>>m, \eta \approx m$

!!! example "计算流水线性能"

    === "静态双功能流水线"

        假设我们现在有一个静态双功能流水线，要计算两个向量 A 和 B 的点积。

        <figure>
            <img src="../assets/计算静态双功能流水线的效率1.png" width="70%">
        </figure>

        由于这是**静态流水线**，同一时刻只能做一类事情，一种操作后才能去做另一种操作。这里我们需要先做乘法，排空，再做加法。需要注意的是，第三个乘法需要等前两个乘法的结果相加后，才能开始计算。

        <figure>
            <img src="../assets/计算静态双功能流水线的效率2.png" width="70%">
        </figure>

        最终得到 $T_p = 7/15 \Delta t, Sp = 1.6, \eta = 32%$

    === "动态双功能流水线"
        
        使用动态流水线时可以在同一时刻执行不同类型的操作，因此不需要等待。

        <figure>
            <img src="../assets/计算动态双功能流水线的效率1.png" width="65%">
        </figure>

        <figure>
            <img src="../assets/计算动态双功能流水线的效率2.png" width="65%">
        </figure>

!!! tip
    流水线的级数并不是越多越好，当流水线的级数过多时会导致

    - 设计复杂度大大增加
    - 需要大量处理在执行中指令之间可能存在的依赖关系
    - 控制逻辑会变得非常庞大和复杂







