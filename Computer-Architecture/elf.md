### Object file


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f5e7ae2400fe42d695c467f41a296666~tplv-k3u1fbpfcp-watermark.image?)

目标文件是【程序】的二进制格式的表现形式，主要有三种类型：

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

可见，目标文件要么用于链接（linking），要么用于执行（execution）

### Object file format

目标文件有很多种格式规范，ELF（Executable and Linking Format）是其中的一种。ELF 最早作为 [Unix ABI 规范](http://www.sco.com/developers/devspecs/gabi41.pdf)的一部分发布，1999 年被选定为运行于 x86 架构上的 Unix 和类 Unix 操作系统的标准二进制文件格式规范。该规范定义了一种与特定 CPU 架构和指令集解耦的、可移植（portable）、可扩展（extensible）的通用规范，各操作系统或硬件平台（处理器）可基于该规范进行拓展

> 本文基于的规范：[ARM ELF](https://refspecs.linuxfoundation.org/elf/ARMELF.pdf)

ELF 文件被划分为四部分：Header、Sections/Segments、Section Header Table、Program Header Table

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fe565dd259e24449b3fcb5a851fc5118~tplv-k3u1fbpfcp-watermark.image?)

ELF Header 描述了文件的核心属性与结构全景，是必不可少的。源代码编译后的数据和指令存储在多个不同类型的**段**（Sections/Segments），每个段的基本属性和位置由**段表**（Section/Program Header Table）描述

之所以划分两种视角（链接视角、执行视角），是因为目标文件既用于链接，又用于执行，ELF 格式兼容两者（从名字即可看出），故有两种视角（文件内容只有一份）

理解目标文件，主要是理解以下三个主题：

- 理解文件内容（二进制串）的格式规范，特别是三个 Header 结构中各字段的含义
- Section 是怎么来的？链接器如何使用 Section 生成其他目标文件？
- Segment 是怎么来的？操作系统如何使用 Segment 创建进程、执行程序？

理解目标文件后，可以回答以下问题：

- 为什么 IDA 反编译出的 so 是以多个不同种类的段的形式呈现的？
- 为什么 frida 可以通过函数名称找到函数地址（findExportByName）？
- so 中的字符串都在哪里？

### ELF Header

文件最开始的一段内容是 ELF Header，格式如下（以 32 位平台为例）

- e_ident
    - 大小：16 字节
    - 含义：文件定义
        - 前 4 个字节：EI_MAGIC，魔数，固定的 `elf`
        - 接下来 1 个字节：EI_CLASS，位数，1 表示 32 位，2 表示 64 位
        - 接下来 1 个字节：EI_DATA，数据编码、大小端
        - 接下来 1 个字节：EI_VERSION，ELF 版本
        - 剩余字节：用 0 占位，以备后用
- e_type
    - 大小：2 字节
    - 含义：表示文件格式（1 表示可重定位文件，2 表示可执行文件，3 表示共享目标文件...）
- e_machine
    - 大小：2 字节
    - 含义：CPU 架构
- e_version
    - 大小：4 字节
    - 含义：ELF 版本
- e_entry
    - 大小：4 字节
    - 含义：程序入口地址
- e_phoff
    - 大小：4 字节
    - 含义：Program Header Table 的起始位置
- e_shoff
    - 大小：4 字节
    - 含义：Section Header Table 的起始位置
- e_flags
    - 大小：4 字节
    - 含义：与特定处理器相关的标志位
- e_ehsize
    - 大小：2 字节
    - 含义：ELF Header 的大小
- e_phentsize
    - 大小：2 字节
    - 含义：Program Header Table 单个 entry 的大小（所有 entry 大小一致）
- e_phnum
    - 大小：2 字节
    - 含义：Program Header Table 的 entry 数量
- e_shentsize
    - 大小：2 字节
    - 含义：Section Header Table 单个 entry 的大小（所有 entry 大小一致）
- e_shnum
    - 大小：2 字节
    - 含义：Section Header Table 的 entry 数量
- e_shstrndx
    - 大小：2 字节
    - 含义：段的索引，指向段表字符串表（`.shstrtab`，本质就是一个段，段表用到的字符串专门保存在这个段）

总计：52 字节

注：Section Header Table 可视为多个 header 构成的数组，header 又称 entry，每个 header 的数据结构完全相同。Program Header Table 同理

找一个 32 位共享库类型的文件，用 `readelf -h` 命令查看文件头信息：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d6225a1608e84c5eb9c6745cc8eb47da~tplv-k3u1fbpfcp-watermark.image?)

文件前 16 个字节如下：

```
7F 45 4C 46 01 01 01 00  00 00 00 00 00 00 00 00
            ↓  ↓
         32 位 小端
```

接下来 16 个字节如下：

```
03 00 28 00 01 00 00 00  00 00 00 00 34 00 00 00
```

因为是小端，e_type 的值 `03 00` 实际是 `00 03`，表示共享目标文件类型

e_machine 的值是 `00 28`，即十进制的 40，参考[这个列表](https://code.woboq.org/userspace/glibc/elf/elf.h.html)，可得 40 表示 `ARM`

e_version 的值是 `00 00 00 01`，即十进制的 1

e_phoff 的值是 `00 00 00 34`，即十进制的 52，确实是 ELF Header 结束、程序头（Program Header Table）开始的地方

接下来 16 个字节是：

```
AC C8 06 00 00 02 00 05  34 00 20 00 08 00 28 00
```

e_shoff 的值是 `00 06 C8 AC`，即十进制的 444588，和用 `readelf -h` 命令查出来的值一样

e_flags 的值是 `05 00 02 00`，具体含义略

e_ehsize 的值是 `00 34`，即十进制的 52，32 位的 ELF Header 大小的确是 52 字节

e_phentsize 的值是 `00 20`，即十进制的 32，表示 Program Header 单个 entry 的大小为 32 字节

e_phnum 的值是 `00 08`，表示 Program Header 有 8 个 entry

e_shentsize 的值是 `00 28`，表示 Section Header 单个 entry 的大小为 40 字节

接下来 4 个字节是：

```
1C 00 1B 00
```

e_shnum 的值是 `00 1C`，表示 Section Header 有 28 个 entry

e_shstrndx 的值是 `00 1B`，表示索引为 27

用 `readelf -S` 命令查看，确实共有 28 个 entry，索引为 27 的段，确实是段表字符串表（.shstrtab => section header string table）

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5a55035fe05949fc872d1ef2f49877f6~tplv-k3u1fbpfcp-watermark.image?)

注：尽管索引为 0 的段没有意义，但仍然在段表中占据一个 entry 的空间


### Section Header Table

Section 的划分是针对链接器而言的，只有链接器会按照 Section 的视角来处理文件内容

每个 Section 一定对应唯一的 header/entry，反之并不成立（某些 header 独立存在，但没有对应的 section）

每个 Section 都在文件中占据**连续的一段空间**

每个 Section 都占据**独立的一块空间**，不存在交叉部分

Section 之间可能存在“空隙”，目标文件可能存在部分无用的空间

#### Section Header 的结构

header/entry 描述了一个 Section 的属性：

- sh_name
    - 大小：4 字节
    - 含义：段名。值为段表字符串表中的索引，指向一个字符串
- sh_type
    - 大小：4 字节
    - 含义：段的类型
- sh_flags
    - 大小：4 字节
    - 含义：权限标志位。1 表示可写，2 表示需要分配内存，4 表示可执行；3 表示 1 + 2，以此类推
- sh_addr
    - 大小：4 字节
    - 含义：段的虚拟地址。如果该段不会被加载到内存，则值为 0
- sh_offset
    - 大小：4 字节
    - 含义：段在文件中相对于文件起始位置的偏移地址
- sh_size
    - 大小：4 字节
    - 含义：段的大小，值是字节数
- sh_link
    - 大小：4 字节
    - 含义：仅与链接有关，如果段的类型和链接无关，则该字段无意义
- sh_info
    - 大小：4 字节
    - 含义：仅与链接有关，如果段的类型和链接无关，则该字段无意义
- sh_addralign
    - 大小：4 字节
    - 含义：段地址对齐约束，值表示 2 的多少次方
- sh_entsize
    - 大小：4 字节
    - 含义：某些段包含一些固定大小的项，如果有，该值表示每一项的大小（字节数）

前文已知，段表起始位置（e_shoff）是 `00 06 C8 AC`，每个 entry 大小（e_shentsize）是 40 字节，所以第 13 个 entry `.plt` 的起始位置是：`06C8AC` + 12 * 40 = `6ca8c`

用命令 `hexdump [示例文件路径] -s 0x6ca8c -e '16/1 "%02x " "\n"'` 读取起始于该地址的 40 个字节：

```
84 00 00 00 --> sh_name，84 即十进制的 132，具体见下文说明
01 00 00 00 --> 1 表示类型为 SH_PROGBITS
06 00 00 00 --> 2 + 4 = 6，表示须分配空间（ALLOC） + 可执行（EXECINSTR）
fc 1e 00 00 --> 段的虚拟地址 1e fc
fc 1e 00 00 --> 段的文件地址偏移 1e fc
e0 03 00 00 --> 段的大小 3e0
00 00 00 00 --> 无链接信息
00 00 00 00 --> 无连接信息
04 00 00 00 --> 地址对齐
00 00 00 00 --> 无固定大小的项
```

可以发现，这个 entry 各字段内容和 `readelf -S` 打印出的信息，的确是符合的

sh_name 是段表字符串表的第 132 个字符，根据段表 header 的描述，段表字符串表起始位置是 `6c791`，大小 `11b` 即 283 个字节，用 `hexdump [示例文件路径] -s 0x6c791 -n 283 -C` 把段表字符串表整个 dump 出来看看：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8487b40acc1848b69ee6f86dfa8e0fa2~tplv-k3u1fbpfcp-watermark.image?)

数一数，索引 132 对应的字符的确是 `.plt`

sh_offset 是 `1efc`，表示整个段的起始位置，在 IDA 里跳转到对应位置发现的确是这里：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c217a617fbd345b18751c9ee7e75b49c~tplv-k3u1fbpfcp-watermark.image?)


#### Section 的类型（sh_type）

主要类型有：

- PROGBITS
    - 值：1
    - 含义：这个 Section 完全由程序决定（program bits）
- SYMTAB
    - 值：2
    - 含义：这个 Section 是一个符号表（symbol table）
- STRTAB
    - 值：3
    - 含义：这个 Section 是一个字符串表（string table）
- RELA
    - 值：4
    - 含义：这个 Section 是一个重定位表。一个目标文件可能有多个重定位表（relocation table）
- HASH
    - 值：5
    - 含义：这个 Section 是一个符号哈希表
- DYNAMIC
    - 值：6
    - 含义：这个 Section 包含动态链接信息
- NOTE
    - 值：7
    - 含义：这个 Section 包含一些提示信息
- NOBITS
    - 值：8
    - 含义：这个 Section 不占空间（no bits）
- REL
    - 值：9
    - 含义：这个 Section 是一个重定位表。一个目标文件可能有多个重定位表
- SHLIB
    - 值：10
    - 含义：保留，未指定
- DYNSYM
    - 值：11
    - 含义：动态链接的符号表（dynamic symbol）

段名和段的类型没有必然联系。段名只是一个代号，在编译和链接过程中有一定意义。真正决定段属性的是段的类型和权限标志位

常见的段有：

- `.data` 数据段，存放的是**已初始化的全局静态变量和局部静态变量**
- `.rodata` 只读数据段，存放的是**只读变量（如 const 修饰的变量和字符串常量）**
- `.text` 代码段，存放的是机器指令（另一个段名是 `.code`）
- `.bss` 存放**未初始化的全局变量和局部静态变量**

它们的类型一般都是 PROGBITS

整体来看，ELF 的段主要有三类：

- 数据段
- 代码指令段
- 其他段：编译器生成的各类符号表、链接信息、注释信息...

### 符号与链接

链接的本质，就是处理【全局符号】的粘合