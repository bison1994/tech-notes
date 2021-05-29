### 名词解释

- PIC = position independent code
- ABI = Application Binary Interface
- EAPI = Embedded Application Binary Interface
- IR = Intermediate Representation
- PIE = Position Independent Executables
- LSB = least significant bit（Little-Endian）
- MSB = most significant bit（Big-Endian）


### object file | executable file

object file 是编译或汇编后的产物，此时还无法直接被 CPU 运行，也不能被其它程序加载

executable file 是链接器（linker）生成的产物，可直接被 CPU 运行

object file 又称 relocatable object file，linux 下扩展名为 `.o`，windows 下扩展名为 `.obj`

object file 除了可被进一步生成可执行文件，还可以用于生成静态库和动态库

- 静态库
    - 本质就是一个或多个 object file 打包（archive）成一个文件
    - linux 下扩展名为 `.a`，windows 下扩展名为 `.lib`
    - 命令示例：`ar rcs xxx.a k.o j.o`
- 动态库
    - 动态库文件，在动态运行期，被动态引用的文件，由一个或多个 object file 生成
    - linux 下扩展名为 `.so`（shared object），windows 下扩展名为 `.dll`（Dynamic link library）
    - 命令示例：`gcc -shared k.o j.o -o xxx.so`


### ABI

Application Binary Interface 是指二进制程序间的接口，主要定义：

- 处理器指令集
- 基本数据类型的规定
- 函数调用规范（calling convention）
- 系统调用（system calls）
- ELF 格式


### ELF

Executable and Linkable Format 是一种针对可执行文件（executable file）、可重定向目标代码（relocatable file）、动态库（shared object file）、core dumps 等文件的通用标准文件格式。最早在 Unix ABI 规范中发布，1999 年被选为运行于 x86 架构上的 Unix 和类 Unix 操作系统的标准二进制文件格式。该标准定义了一种与特定 CPU 架构和指令集解耦的通用规范
