---
    comments: true
    statistics: true
---

# Fundamentals of Computer Design

## Introduction

<figure>
    <img src="../assets/冯诺依曼架构.png" width="80%"/>
</figure>

- 冯诺依曼架构：数据和程序都存储在内存中
- CPU 从内存中取出指令和数据，进行操作后把结果写回内存

### Classed by Flynn

<figure>
    <img src="../assets/福林分类法.png" width="70%"/>
</figure>

- SISD：单指令单数据流，如早期的单核 PC
- SIMD：单指令多数据流，一条指令有多条数据执行，如 GPU
- MISD：多指令单数据流
- MIMD：多指令多数据流

## Performance

影响性能的因素有很多

- Architecture
- Hardware implementation
- Compiler
- Operating system
- Application

我们需要定义一些指标来衡量性能

- 个人使用的 PC -> 最小化响应时间
- 大的数据集 -> 最大化吞吐量

因此我们有了响应时间和吞吐量两个指标

- Latency (Response time)
    一个事件从开始到被完成的时间
- Throughput (bandwidth)
    给定的时间段内完成的工作量

通常我们把性能定义为执行时间的倒数
$$ Performance = \frac{1}{Execution Time} $$

体系结构优化的总目标就是提升系统的性能。

## Quantitative approaches

### CPU Performance

- CPU 执行时间 = CPU 时钟周期数 $\times$ CPU 时钟周期时间 = $\frac{CPU 时钟周期数}{CPU 时钟频率}$
- IC: Instruction Count，指令数
- CPI: Cycles Per Instruction，每条指令的时钟周期数
    - 由 CPU 硬件决定
    - 不同的指令也会有不同的 CPI，平均 CPI 取决于指令的组合方式
    - CPI = CPU 时钟周期数 / IC
    - CPU 执行时间 = IC $\times$ CPI / CPU 时钟频率

!!! example

    - Computer A: 2GHz clock, 10s CPU time
    - Designing Computer B
        - Aim for 6s CPU time
        - Can do faster clock, but causes 1.2 × clock cycles
    - How fast must Computer B clock be?

    $$ \text{Clock Rate}_B = \frac{\text{Clock Cycles}_B}{\text{CPU Time}_B} = \frac{1.2 \times \text{Clock Cycles}_A}{6s} $$
    $$ \text{Clock Cycle}_A = \text{CPU Time}_A \times \text{Clock Rate}_A = 10s \times 2GHz = 20 \times 10^9 \text{cycles} $$
    $$ \text{Clock Cycles}_B = \frac{1.2 \times 20 \times 10^9}{6s} = 4 \times 10^9 \text{cycles} = 4GHz $$

### Amdahl's Law

> The performance improvement to be gained from using some faster mode of execution is limited by the fraction of the time the faster mode can be used.

系统性能被提升的上限取决于系统中可以被优化的部分。

$$ T_{improved} = \frac{T_{affected}}{improvement factor} + T_{unaffected} = T_{unimproved} \times \left( (1 - f) + \frac{f}{s} \right) $$

加速比 = 优化后的性能 / 优化前的性能 = 优化前的执行时间 / 优化后的执行时间

- 执行时间

    $$ T_{new} = T_{old} \times \left( (1 - f) + \frac{f}{s} \right) $$

- 整体加速比

    $$ Speedup = \frac{T_{old}}{T_{new}} = \frac{1}{(1 - f) + \dfrac{f}{s}} $$

## Great Architecture Ideas

1. Design for Moore's Law
    面向摩尔定律设计
2. Use abstraction to simplify design
    使用抽象来简化设计
3. Make the common case fast
    加速常见情况
4. Improve performance via parallelism
    通过并行提升性能
5. Improve performance via pipelining
    通过流水线提升性能
6. Improve performance via prediction
    通过预测提升性能
7. Use a hierarchy of memories
    使用内存层次结构
8. Improve dependability via redundancy
    通过冗余提升可靠性

## ISA

ISA: Instruction Set Architecture 指令集体系结构

从某种角度来看，一个 ISA 就对应着一种计算机体系结构。

Instruction Set Design Basic Principles

- Compatibility 兼容性
- Versatility 通用性
- High efficiency 高效性
- Security 安全性
