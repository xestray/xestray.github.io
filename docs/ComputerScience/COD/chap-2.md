---
    comments: true
    statistics: true
    counter: true
---

# Instructions: Language of the Computer

## Introduction

- Language of the machine

    - Instructions (Statement)
    - Instruction Set (Syntax)

- Design goal

    - Maximize performance

        在同样资源（时间、空间）的情况下尽可能提高性能

    - Minimize cost

        在保证同样性能的情况下尽可能降低成本

    - Reduce design time

        指令集更简单，易于理解和设计

- 我们课程中使用的指令集为 RISC-V

Instruction Characteristics

<figure>
    <img src="../assets/指令集.png" width="50%">
</figure>

指令集的基本结构包括：Operator（操作符）、Operands（操作数）

- 对于不同指令集，指令的编码通常不同，例如可以用 000 表示加法，这也被称为指令的 Encoding（编码）
- 操作数的位宽可能不同，对应的来源也可能不同，可能来自寄存器、内存、立即数等

冯诺依曼架构：指令被认为是一种特殊的数据，和其他数据储存在**同一个内存**中，可以进行读写操作

哈佛架构：指令和数据**分开存储**，分别有自己的内存空间

在实验课中我们实现的实际上是哈佛架构，但在考试中的具体题目也可能是冯诺依曼架构的。

## Operations

- 每一个计算机都需要实现算数运算操作

    - Only one operation per instruction
    - Exactly **3** variables

        例如`add a b c`，表示将`b`和`c`相加，结果存入`a`，$a \gets b + c$

- Design Principle 1 —— **Simplicity favors regularity**

    **简单源自规整**，如每条指令都包含三个操作数

!!! example
    - C code 
    ```C
    f = (g + h) - (i + j);
    ```
    - RISC-V code
    ```
    add t0, g, h
    add t1, i, j
    sub f, t0, t1
    ```

## Operands

### Register Operands

- 算术指令（Arithmetic Instructions）的操作数只能来自寄存器或立即数
- RISC-V 中有 32 个 64-bit 的寄存器，分别用`x0`到`x31`表示

    - 32-bit 的数据被称为一个 word，64-bit 的数据被称为一个 doubleword

| Name    | Register Name | Usage                         | Preserved or call? |
|---------|---------------|-------------------------------|--------------------|
| x0      | 0             | The constant value 0          | n.a.               |
| x1(ra)  | 1             | Return address(link register) | yes                |
| x2(sp)  | 2             | Stack pointer                 | yes                |
| x3(gp)  | 3             | Global pointer                | yes                |
| x4(tp)  | 4             | Thread pointer                | yes                |
| x5-x7   | 5-7           | Temporaries                   | no                 |
| x8-x9   | 8-9           | Saved                         | yes                |
| x10-x17 | 10-17         | Arguments/results             | no                 |
| x18-x27 | 18-27         | Saved                         | yes                |
| x28-x31 | 28-31         | Temporaries                   | no                 |

!!! note "为什么 x0 寄存器的值需要保持为 0 ？"
    Make the common fast.

    在实际的运算中常会有 0 参与，把它单独存放在一个寄存器中，便于计算。

### Memory Operands

使用内存的好处在于可以存储比寄存器更多、更复杂的数据，例如数组或结构体等

- 访问内存的速度通常比访问寄存器慢
- 通过内存地址（Memory Address）判断我们需要的数据储存在内存的哪个位置
- RISC-V 中的内存地址是 **byte addressed** 的，也就是说每个地址对应一个 8-bit 的字节

RISC-V 中的内存访问指令有两种：

- Load：从内存中读取数据，储存到寄存器中
- Store：将寄存器中的数据写入内存

RISC-V 是 **Little Endian（小端序）** 的，也就是说低位字节存储在低地址，高位字节存储在高地址，Big Endian（大端序）则相反 

!!! info "Little vs Big Endian"
    - Little Endian：数据的低位字节存储在低地址
    - Big Endian：数据的高位字节存储在低地址

    例如，对于 32-bit 的数据 `0x01234567`，在内存中的存储方式如下：

    - Little Endian：`67 45 23 01`
    - Big Endian：`01 23 45 67`

    <figure>
        <img src="../assets/小端序与大端序.png" width="75%">
    </figure>

与一些其他的 ISA 不同，RISC-V 不要求 word 在内存中对齐，也就是说一个 word 的数据可以从任意地址开始

- words align：一个 word 是 4 个字节，就要求一个 word 的起始地址一定要是 4 的倍数

!!! note "Memory Alignment"
    <figure>
        <img src="../assets/内存对齐.png" width="75%">
    </figure>

    上图中第一种情况是对齐的，第二种情况是不对齐的

    - 对齐的好处在于可以提高内存访问的效率，比如能够保证不需要读取两次才能得到一个 word 的数据
    - 不对齐的好处在于节省空间

!!! example "Memory Operand Example"
    - C code
    ```C
    g = h + A[8];
    ```
        
        这里我们默认数组的元素都是 doubleword 的，并且 assume g in `x20`, h in `x21`, base address of A in `x22`

    - RISC-V code
    ```c
    ld x9, 64(x22)
    add x9, x21, x9
    sd x9, 96(x22)
    ```

    因为地址以 byte 为单位，且 1 doubleword = 8 byte，那么我们在根据访问内存中相应的数据时需要偏移 $8 \times 8 = 64$ bytes.

### Registers vs. Memory

- Registers are faster to access than memory

    寄存器中的数据可以直接取出来就用，但内存中的数据需要先根据内存地址找到数据所在位置，然后传输回运算单元

- Operating on memory data requires loads and stores

    使用内存中的数据需要执行额外的指令才能取出/写回

- Compiler must use registers for variables as much as possible

    编译器尽量使用寄存器存变量。只有在寄存器不够用时，或者使用寄存器无法保存的数据（数组、结构体等）时，才会选择用内存。

### Constant or Immediate Operands

除了使用寄存器或内存中的数据之外，我们还可能使用到常量数字或者立即数（Immediate），如 $ h = h + 5 $ ``addi x22, x22, 5``

Immediate：Other method for offering operands

!!! summary
    <figure>
        <img src="../assets/操作数.png" width="90%">
    </figure>

    - 为什么内存是 61 个 doubleword？

    因为 RISC-V architecture 的地址是 64 位的，因此总共可以寻址 $2^{64}$ 个地址，每个地址对应一个 byte，因此总共有 $2^{64}$ 个 byte，也就是 $2^{64} / 8 = 2^{61}$ 个 doubleword。因此我们`load`和`store`指令可以访问的范围就是这 $2^{61}$ 个 doubleword。

## Signed and Unsigned Number

这部分就是老生常谈的二进制反码和补码的知识，就不再重复了

## Representing Instructions in the Computer

计算机中的所有信息都使用二进制来表示，指令也不例外，指令会被编码成二进制的形式，称为机器码（Machine Code）。

!!! note "RISC-V instructions"
    所有的 RISC-V 指令都是都会被编码成规整的 32-bit，一部分是 opcode，一部分是 operands 等等。

<figure>
    <img src="../assets/RISC-V指令.png" width="100%">
</figure>

### R-format

<figure>
    <img src="../assets/R型指令.png" width="80%">
</figure>

- **opcode**: basic operation and format of an instruction.
- **rd**: the register destination operand.
- **funct3**: an additional opcode field.
- **rs1**: the first register source operand.
- **rs2**: the second register source operand.
- **funct7**: an additional opcode field.

### I-format

<figure>
    <img src="../assets/I型指令.png" width="80%">
</figure>

包括立即数算术运算和`load`指令

- **rs1**: source or base address register number
- **immediate**: constant operand, or offset added to base 
address
    - 立即数是二进制补码表示的有符号数
    - 相当于把 rs2, funct7 合并了，得到一个 12-bit 的立即数

### S-format

<figure>
    <img src="../assets/S型指令.png" width="80%">
</figure>

- **rs1**: base address register number
- **rs2**: source operand register number

    rs2 寄存器的数据就是将要写入内存的数据

- **immediate**: offset added to base address
    - 这里需要注意，S-format 指令的立即数在指令中的位置和 I-format 是不同的
    - 立即数的位置被分割成了两部分，这样能保证 rs1 和 rs2 所在的位置保持不变

??? example
    <figure>
        <img src="../assets/S型指令例子1.png" width="80%">
    </figure>

    <figure>
        <img src="../assets/S型指令例子2.png" width="80%">
    </figure>

!!! note "Stored Program in Computer"
    <figure>
        <img src="../assets/程序储存在内存中.png" width="80%">
    </figure>

    - 事实上指令和程序和数据一样，都会被表示为二进制的形式，储存在内存中
    - 有的程序可以对别的程序进行操作
        - 例如编译器（Compiler）、连接器（Linker）等
    - Binary compatibility allows compiled programs to work on different computers

        二进制的兼容性使得编译好的程序可以在不同的计算机上运行

## Logical Operations

| Operation     | C  | Java | RISC-V    |
|---------------|----|------|-----------|
| Shift left    | << | <<   | slli      |
| Shift right   | >> | >>   | srli      |
| Bit-by-by AND | &  | &    | and, andi |
| Bit-by-by OR  | \| | \|   | or, ori   |
| Bit-by-by XOR | ^  | ^    | xor, xori |
| Bit-by-by NOT | ~  | ~    | -         |

RISC-V 中的按位 NOT 指令需要通过异或来实现，如`xori x2, x2, -1`

### Shift

- 将二进制数据向左或向右移动，
- 左移时在右侧补 0，右移时在左侧补 0 或符号位，需要根据是逻辑右移还是算术右移来决定
- 左移 左移 i 位相当于乘 $2^i$, 右移 i 位相当于除 $2^i$
- 通常 shift 指令使用的是 I 型指令，`slli`、`srli`、`srai`

    <figure>
        <img src="../assets/shift指令.png" width="80%">
    </figure>

    - 为什么还会有一个`funct6`？

        因为 RISC-V 的数据至多是 doubleword，也就是 $64 = 2^6$ 位，因此左移或者右移的立即数只需要 6 位就可以表示

### AND、OR、XOR

可能是寄存器和寄存器的数据之间按位运算，也可能是寄存器的数据与立即数按位运算

## Instructions for Making Decisions

### Branch instructions

- `beq rs1, rs2, label`

    如果`rs1`和`rs2`相等，那么跳转到`label`处

- `bne rs1, rs2, label`

    如果`rs1`和`rs2`不相等，那么跳转到`label`处

除了上面两种之外，还有一些其他的条件跳转指令，如`blt`、`bge`、`bltu`、`bgeu`等

??? example 
    === "判断语句"
        <figure>
            <img src="../assets/跳转语句.png" width="70%">
        </figure>

    === "循环跳转"
        <figure>
            <img src="../assets/循环跳转语句.png" width="70%">
        </figure>

branch 类型指令的立即数是作为地址偏移量与当前指令的 PC 相加来计算需要跳转到的地址的

- 由于立即数只有 12 位，跳转的范围有限，即 $-2^{11} \leq \text{offset} \leq 2^{11} - 1$=

!!! note "index out of bounds 检测"
    `If (x20 >= x11 || x20 < 0) goto IndexOutofBounds;`
    
    上面的这条指令可以只需要一条指令就可以实现：
    ```
    bgeu x20, x11, IndexOutofBounds
    ```
    > 需要注意的是，这里我们默认 x11 中存的值大于0，否则上面这个判断条件无论如何都是 True，这样就没有意义了

    如果 x20 > 0，那么两个数都是整数，用 bgeu 也不会有问题；如果 x20 < 0，那么由于 x20 用二进制补码来表示，这时候它又会被视为一个很大的正数，因此`bgeu x20, x11`也是成立的。

### slt instruction

slt：Set on Less Than

- `slt rd, rs1, rs2`

    如果`rs1` < `rs2`，那么`rd` = 1，否则`rd` = 0

此外，还有

- slti：Set on Less Than Immediate
- sltu：Set on Less Than Unsigned
- sltiu：Set on Less Than Immediate Unsigned

### Case/Switch

`jalr rd rs1 imm` 或 `jalr rd imm(rs1)`

jalr 指令是 I 型指令，具体操作是从寄存器中取出数据，然后把这个数据与一个立即数相加，跳转到这地址去，同时把下一条指令的地址，即 PC+4 写入 rd 寄存器中。

假如我们不需要下一条指令的地址，我们就把 rd 寄存器设置为 x0，因为 x0 寄存器的值始终是 0，不会被修改。

!!! example "Compiling a switch using jump address table"
    <figure>
        <img src="../assets/跳转地址表1.png" width="80%">
    </figure>

    <figure>
        <img src="../assets/跳转地址表2.png" width="70%">
    </figure>

    <figure>
        <img src="../assets/跳转地址表3.png" width="70%">
    </figure>

    在上面的这个例子中，编译器实际上会把 switch 语句语句中不同情况的程序内容写到内存不同的位置，然后把这些地址用一个表来存储。当程序执行到 switch 语句时，会先找到这个地址表的位置，然后从表中读取所需要的地址，然后再把这个地址对应的内容从内存里读取出来。

!!! note "Basic Blocks"
    A **basic block** is a sequence of instructions with

        - No embedded branches (except at end)
        - No branch targets (except at beginning)

    我们把一些不含有分支跳转语句同时也不会作为跳转目标的指令的集合称为**基本块**，基本块是编译器优化的基本单位。

## Supporting Procedures in Computer Hardware













## Communicating with People






## RISC-V Addressing for Wide Immediate and Addresses











## Parallelism and Instructions: Synchronization







## Parallelism and Instructions: 










































