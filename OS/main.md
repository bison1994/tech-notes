# 操作系统的两个核心作用

- 向下：管理硬件资源，主要目标是充分的利用资源，以实现最大的计算吞吐量
  - 内存管理：虚拟内存机制
  - 设备管理：通过设备驱动，以【内存映射输入输出】和【中断】这两种主要方式进行交互
- 向上：管理应用以及为应用提供服务
  - 三个层次的接口：系统调用、POSIX 接口、领域应用接口
    - 系统调用：允许内核向用户程序暴露某些用户切实需要但不允许直接调用的特权功能，例如访问文件系统、创建和销毁进程、与其他进程通信，以及分配更多内存等。大多数操作系统提供几百个调用
  - 重点是通过用户态、内核态进行权限隔离，确保操作系统对进程具有完全的限制、控制能力


# 操作系统的演化

- 批处理（多道程序）：提供简单粗糙的调度服务，支持输入一批任务，通过自动调度，将所有任务执行完，且能够交叉运行（一个程序暂时无需使用 cpu 时自动切到另一个程序）以提高并发效率
- 分时（多任务）：更精细的调度，如进程、时间片、优先级等


# 内核架构

- 简要结构：应用和内核共享同一个地址空间，应用程序可以直接调用内核函数，无专门的内存管理
- 宏内核：在内存管理上划分了用户态和内核态
- 微内核：为解决过多系统功能集中在内核中导致复杂度失控以及因缺乏隔离导致健壮性和安全性较差的问题，将内核功能进行精简，拆分出的模块作为服务单独部署在独立的空间
- 外核架构：某些场景下，操作系统以内核形式提供硬件资源管理不一定是最合适的，更适合以库的形式和应用程序直接链接
- 多内核架构：多个内核以进程通信方式连接成的分布式系统
- 混合内核


# 复杂度控制的通用原则

- 模块化
- 抽象
  - 隔离、解耦
  - 隐藏、内化复杂度
- 分层
  - 外部分层
  - 内部分层

# 线程

### Thread

- Instruction stream with state (registers and memory)
- Register state is also called “thread context”
- Threads in the same program share the same address space (shared memory model)

### 硬件线程

- 硬件线程，单个内核并行执行多指令（超线程）
- 目的：提高 CPU 运算吞吐（throughput）
  - 起因：某些指令时延较高，这段时间可用于执行其他指令
  - 原理：常规处理器线程切换通常需要 20000 个时钟周期，超线程处理器可在每个时钟周期决定执行哪个线程的指令
  - 超标量：速率大于一个时钟周期一条指令
- 三种类型：
  - 细粒度（Fine-grained）：
  - 粗粒度（Coarse-grained）：
  - 同步（Simultaneous）：

> [multithreading.pdf](https://course.ece.cmu.edu/~ece740/f13/lib/exe/fetch.php?media=seth-740-fall13-module6.1-multithreading.pdf)

# 嵌入式系统

类似于程序语言中的 DSL，针对特定硬件环境设计的系统