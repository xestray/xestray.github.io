---
    comments: true
    statistics: true
---

# DLP and TLP

!!! abstract "Flynn 分类法"
    Flynn 分类法按照指令和数据流不同的组织方式，将计算机系统分为四类：

    - SISD（Single Instruction Single Data）：单指令单数据流，传统的单核处理器。
    - SIMD（Single Instruction Multiple Data）：单指令多数据流，适用于向量处理器和图形处理器。
    - MISD（Multiple Instruction Single Data）：多指令单数据流，较少见，通常用于冗余计算。
    - MIMD（Multiple Instruction Multiple Data）：多指令多数据流，现代多核处理器。

    <figure markdown="span">
        ![](./assets/数据级并行1.png){width=80%}
    </figure>

## SIMD: vector processor

### Vector Processor & Scalar Processor 

- **向量处理器（vector processor）**：具有向量数据表示和相应的向量指令的*流水线处理器*。
- **标量处理器（scalar processor）**：没有向量数据表示和相应的向量指令的*流水线处理器*。

它们通常有三种处理数据的模式：

1. Horizontal processing method
    - Vector calculations are performed horizontally from left to right in a row.
        - 每行从左到右横向计算，一行计算完毕后才开始下一行。
    - Problems with horizontal processing:
        - 每部分计算都会出现 RAW（Read After Write）数据依赖，流水线效率低下
        - 若使用静态多功能流水线（static multi-functional pipeline），每次切换功能部件都需要排空流水线，这样的效率甚至不如顺序执行
        - horizontal processing method 不适用于向量处理器
2. Vertical processing method
    - The vector calculation is performed vertically from top to bottom in a column manner
3. Vertical and horizontal processing method (group processing method)
    - 把上面两种方法结合起来

!!! example
    考虑计算 $D = A \times (B+C)$，向量长度为 $N$

    - Horizontal processing method
        - 分成 $N$ 个 scalar operations，逐行计算
        - 先计算 $d_1 \leftarrow a_1 \times (b_1 + c_1)$，然后计算 $d_2 \leftarrow a_2 \times (b_2 + c_2)$，依次类推
        - 计算过程可以写作：
            - $k_i \leftarrow b_i + c_i$
            - $d_i \leftarrow a_i \times k_i$
        - 会出现 N 次数据相关，需要 2N 次功能切换
    - Vertical processing method
        - 先计算向量加法，再计算向量乘法
        - 计算过程可以写作：
            - $K \leftarrow B + C$
            - $D \leftarrow A \times K$
        - 只会出现 1 次数据相关，和 2 次功能切换
    - Vertical and horizontal processing method
        - 如果 N 很大，一个向量无法容纳所有数据，那么数据分成多个组
        - $N = S \times n + r$，即分成 $S$ 组，每组 $n$ 个元素，最后剩下的一组可能有 $r$ 个元素
        - 组内做纵向计算，组间做横向计算
        - S+1 次数据相关，和 2(S+1) 次功能切换


- 方法二操作要求处理器结构为 **memory-memory structure**：
    - 源向量和目标向量都要存储在内存中，计算的中间结果也要写回到内存里

    <figure markdown="span">
        ![](./assets/数据级并行2.png){width=80%}
    </figure>

- 方法三操作要求处理器结构为 **register-register structure**：
    - 使用可以快速访问的向量寄存器，用于存储源向量、目标向量和中间结果
    - 计算组件的输入输出端直接与向量寄存器相连，中间结果直接存储在寄存器中

### Vector Processor Example -- Cray-1

<figure markdown="span">
    ![](./assets/数据级并行3.png){width=80%}
</figure>

- 包含 8 个向量寄存器，每个寄存器可以存储 64 个 64 位的浮点数，寄存器之间通过向量总线连接。
- 有 12 条可以并行执行的单功能流水线

<figure markdown="span">
    ![](./assets/数据级并行4.png){width=65%}
</figure>

- 每个向量寄存器 $V_i$ 都有单独的一个总线，连接至 6 个功能部件
- 每个向量功能部件也有一个总线，用于将计算结果返回给向量寄存器总线
- 当没有出现 $V_i$ 冲突和功能冲突时，每个向量寄存器 $V_i$ 和每个功能部件都可以并行工作

!!! info
    - **$V_i$ conflict**: The source vector or result vector of each vector instruction working in parallel uses the same $V_i$

        - 即向量寄存器之间的使用存在依赖，后续指令需要等待前面指令的执行完成后再执行
        - 向量元素级别的等待，即前面的指令的第一个元素计算完成后，就开始计算后面指令的第一个元素
        - Writing and reading data related
            $$ V0 \leftarrow V1 + V2 $$
            $$ V3 \leftarrow V0 + V4 $$
        - Reading data related
            $$ V0 \leftarrow V1 + V2 $$
            $$ V3 \leftarrow V1 \times V4 $$

    - **Functional conflict**: Each vector instruction working in parallel must use the same functional unit

        - 多条指令需要使用同一个功能部件
        - 只能等待前面的指令彻底执行完毕后（最后一个元素计算完成）才能开始执行后续指令
        - 例如当我们只有一个乘法单元时，下面的指令就会出现功能冲突：
            $$ V0 \leftarrow V1 + V2 $$
            $$ V3 \leftarrow V4 \times V5 $$

#### Instruction Types of CRAY-1

<figure markdown="span">
    ![](./assets/数据级并行5.png){width=70%}
</figure>

向量加法需要 6 个周期，乘法需要 7 个周期，内存读写需要 6 个周期。

#### Improve the Performance of Vector Processor

- Set up multiple functional components and make them work in parallel.
- Use vector chaining technology to speed up the execution of a string of vector instructions.
- Adopt recycling mining technology to speed up recycling processing.
- Using a multi-processor system to further improve the performance.

第 1、3、4 种方法实际上都是增加硬件资源，在这里我们主要介绍第 2 种方法

!!! tip "Vector Chaining"
    - 向量链接（vector chaining）是指将一个向量指令的结果直接作为下一个向量指令的输入，而不需要将结果写回到寄存器或内存中。
    - 例如我们有两条指令，第一条指令的计算结果是第二条指令的输入。那么我们就可以把这两条指令链接起来，第一条指令每计算出一个元素，就立即传递给第二条指令，从而实现类似于流水线的连续执行。

!!! example
    考虑 $D = A \times (B+C)$，假设向量长度 $N \leqslant 64$，元素均为浮点数，$B$ 和 $C$ 分别存储在寄存器 $V_0$ 和 $V_1$ 中

    我们可以按照如下的方法计算：

    ```asm
    V3 <- memory    // access vector A
    V2 <- V0 + V1   // Vector B and Vector C perform floating point addition
    V4 <- V3 * V2   // Floating point multiplication, the result is stored in V4
    ```

    前两条指令不存在冲突，可以并行执行；第三条指令 RAW 依赖于前两条指令，但是我们可以将它们连接起来

    <figure markdown="span">
        ![](./assets/数据级并行6.png){width=70%}
    </figure>

    > 假设把向量元素数据送到功能部件、把向量元素数据存储到向量寄存器中、把数据从内存中送到 fetch function unit 里都需要一拍

    - 路径一：1 拍从内存到 fetch function unit，6 拍进行取数据操作，1 拍把取出的数据送至 V3
    - 路径二：1 拍将数据从 V0 和 V1 送到加法单元，6 拍进行加法操作，1 拍把加法结果送至 V2

    两条路径花费的时间都是 8 拍，因此在第 9 拍乘法单元就可以开始计算

!!! question 
    考虑下面的指令，它们在不同情况下执行花费的总时间是多少？

    ```asm
    V3 <- memory
    V2 <- V0 + V1
    V4 <- V3 * V2
    ```

    1. 指令顺序执行

        经过 8 拍后第一个元素从内存中被取出，在经过 N-1 拍后所有元素都被取出，共需要 (1+6+1)+N-1 拍，后面的两条指令类似。

        于是我们可以知道总共需要的拍数为
        $$[(1+6+1)+N-1] + [(1+6+1)+N-1] + [(1+7+1)+N-1] = 3N+22 $$

    2. 并行执行前两条指令，然后再执行最后一条指令

        需要的拍数为：
        $$ \max\{ [(1+6+1)+N-1], [(1+6+1)+N-1] \} + [(1+7+1)+N-1] = 2N+15 $$

    3. 采用向量链接技术

        我们只需要直到 V4 的第一个元素需要多长时间被计算出来即可：(1+6+1)+(1+7+1)=17 拍，随后还有 N-1 个元素需要计算，因此总拍数为 N+16

