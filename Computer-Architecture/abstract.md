### 名词解释

- PIC = position independent code
- ABI = Application Binary Interface
- EAPI = Embedded Application Binary Interface
- IR = Intermediate Representation
- PIE = Position Independent Executables
- LSB = Least Significant Bit（Little-Endian）
- MSB = Most Significant Bit（Big-Endian）
- ISA = Instruction Set Architecture
- LSB = Linux Standard Base
- STL = Standard Template Library


### object file

目标文件是程序的二进制格式的表现形式，主要有三种类型：

- re-locatable file（可重定位目标文件）
    - re-locatable file 是编译或汇编后的产物，代码地址从 0 开始，无法直接被 CPU 运行，也不能被其它程序加载，只能用作生成 executable file 或 shared object file 的原料
    - linux 下扩展名为 `.o`，windows 下扩展名为 `.obj`
- executable file（可执行目标文件）
    - executable file 是链接器（linker）生成的产物，可直接被 CPU 运行
    - [可执行文件扩展名](https://fileinfo.com/filetypes/executable)有很多
- shared object file（动态库文件）
    - 动态库文件由一个或多个 object file 生成，可在运行时被动态加载与执行。相比静态库，动态库在跨应用程序的复用上具备许多优势
    - linux 下扩展名为 `.so`（shared object），windows 下扩展名为 `.dll`（Dynamic link library）
    - 生成命令示例：`gcc -shared a.o b.o -o output.so`

可见，目标文件要么用于链接（program linking），要么用于执行（program execution）

### ABI

Application Binary Interface 是指供二进制程序使用的接口，主要定义：

- 处理器指令集
- 基本数据类型的规定
- 函数调用规范（calling convention）
- 系统调用（system calls）
- ELF 格式


### ELF

Executable and Linkable Format 是一种针对可执行文件（executable file）、可重定向目标代码（relocatable file）、动态库（shared object file）、core dumps 等文件的通用标准文件格式。最早作为 [Unix ABI 规范](http://www.sco.com/developers/devspecs/gabi41.pdf)的一部分发布，1999 年被选定为运行于 x86 架构上的 Unix 和类 Unix 操作系统的标准二进制文件格式。该标准定义了一种与特定 CPU 架构和指令集解耦的、可移植（portable）、可扩展（extensible）的通用规范，各操作系统或硬件平台（处理器）基于该规范进行了定向拓展

> [TIS ELF 规范 1.1](https://www.cs.cmu.edu/afs/cs/academic/class/15213-f00/docs/elf.pdf)

> [TIS ELF 规范 1.2](https://refspecs.linuxfoundation.org/elf/elf.pdf)

> [ARM ELF 规范](https://refspecs.linuxfoundation.org/elf/ARMELF.pdf)

按照 object file 的用途，ELF 定义了两类格式：

- Linking View
  - ELF Header
  - Program Header Table（optional）
  - Sections
  - Section Header Table
- Execution View
  - ELF Header
  - Program Header Table
  - Segments
  - Section Header Table（optional）
  
![](https://web.archive.org/web/20150602071342/http://nairobi-embedded.org/img/elf/elf_link_vs_exec_view.jpg)

这样划分的意义是：链接器操作的目标是 section，而加载执行操作的目标是 segment。一般来说，一个 segment 包含多个 section


除了 ELF Header 位置固定在顶部，其他部分的次序在不同文件中不一定相同

**ELF Header** 主要描述了文件的元信息和结构信息，包括 magic number、32 位/64 位、文件类型、CPU 架构、程序入口/起点地址、ELF Header 大小、Header Table 的入口数量/大小和偏移位置等

可用 readelf 命令查看 elf 结构

> [understanding-the-elf](https://medium.com/@MrJamesFisher/understanding-the-elf-4bd60daac571)

**Program Header Table（PHT）** 由一组数据结构组成，每一组描述一个 segment 或其他系统执行程序所必须的信息。仅 executable 和 shared object file 有该模块（这两种文件本质上都是可直接被 CPU 执行的，它们的代码已经静态的呈现了最终的程序，只不过一个是直接执行，一个是加载执行）

```
Type           Offset   VirtAddr   PhysAddr   FileSiz MemSiz  Flg Align
  PHDR           0x000034 0x00000034 0x00000034 0x00100 0x00100 R   0x4
  LOAD           0x000000 0x00000000 0x00000000 0xc1e88 0xc1e88 R E 0x1000
  LOAD           0x0c2c20 0x000c3c20 0x000c3c20 0x07608 0x223d9 RW  0x1000
  DYNAMIC        0x0c5790 0x000c6790 0x000c6790 0x00128 0x00128 RW  0x4
  NOTE           0x000134 0x00000134 0x00000134 0x000bc 0x000bc R   0x4
  GNU_STACK      0x000000 0x00000000 0x00000000 0x00000 0x00000 RW  0x10
  EXIDX          0x0b9598 0x000b9598 0x000b9598 0x04478 0x04478 R   0x4
  GNU_RELRO      0x0c2c20 0x000c3c20 0x000c3c20 0x033e0 0x033e0 RW  0x8
```

**Sections / Segments** 是程序的主体，包括指令、数据、符号表等信息。每个段都是连续的字节序列。系统创建进程映像（process image）的过程，本质就是把 segment 复制、载入到内存

**Section Header Table（SHT）** 由一组大小相同的入口（段表项）组成，描述了每个段的名称、大小等信息。段和段表项是一一对应的


### 编译的四个阶段

- 预处理：处理所有 # 号开头的内容
- 编译：源代码到汇编
- 汇编：汇编到机器码
- 链接
