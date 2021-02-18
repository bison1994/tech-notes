# shell 脚本的构成

shell 脚本只有以下五种东西：

- 命令
- keyword（例如 if、function）
- 变量
- 符号
- 字符串


# shell 命令的基本概念

命令的基本单位是“行”

一行命令的基本结构是：command + [options]

第一个词汇，一定是命令

命令只有以下四种类型：

- 可执行程序
  - 直接指向某个文件：绝对路径 / 相对路径
  - 一个单词，系统自动添加 path 后组成绝对路径
- shell 内置命令（builtins）
- shell 函数（function）
- alias
