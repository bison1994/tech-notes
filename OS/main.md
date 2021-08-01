# 计算机组成

- 硬件线程，单个内核并行执行多指令（superscalar，超标量）
- 目的：提高 CPU 运算吞吐（throughput）
  - 起因：某些指令时延较高，这段时间可用于执行其他指令
- 三种类型：
  - 细粒度（Fine-grained）：
  - 粗粒度（Coarse-grained）：
  - 同步（Simultaneous）：

> [multithreading.pdf](https://course.ece.cmu.edu/~ece740/f13/lib/exe/fetch.php?media=seth-740-fall13-module6.1-multithreading.pdf)

# 线程

Thread
- Instruction stream with state (registers and memory)
- Register state is also called “thread context”
- Threads in the same program share the same address space (shared memory model)

# 嵌入式系统

类似于程序语言中的 DSL，针对特定硬件环境设计的系统