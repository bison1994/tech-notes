# shell 脚本的构成

shell 脚本只有以下内容元素：

- shebang
- 命令
- keyword（例如 if、function）
- 符号
  - 有特定语义的符号（例如 $、括号）
  - 分隔符（例如空格、分号）
- 字符
- 注释

<br>

# shell 命令的基本概念

命令的基本单位是“行”

一行命令的基本结构是：command + [options]

第一个单词，一定是命令

命令只有以下四种类型：

- 可执行程序
  - 直接指向某个文件：绝对路径 / 相对路径
  - 一个单词，系统自动添加 path 后组成绝对路径
- shell 内置命令（builtins）
- shell 函数（function）
- alias

<br>

# shell 脚本的解析机制

shell 脚本不过是命令的集合，凡是能够在命令行中运行的命令，都可以写到 shell 脚本中

默认情况下，解释器将文本划分为行，每一行视为一个命令，从上到下依次执行

每行命令又被分割为一个个单词，除了第一个单词被视为命令，其他单词均视为普通文本

例如 1 + 1 在 shell 中会被视为 `1 + 1` 文本，只有通过特殊符号告诉 shell 这段文本是算数表达式，shell 才会执行计算

> 空格用于划分单词，不能随便加，例如 `{a,c}` 和 `{a, c}` 是完全不一样的

此外，shell 脚本还提供了一些基本的程序语言能力，包括：

- 字符展开（含变量）
- 函数（含局部变量）
- 流程控制
- 数组

<br>

### 分号

shell 将分号视为 command seperator

命令行：顾名思义，命令以行为单位。默认以 `\n` 作为划分行的分隔符

如果多个命令写在一行，就需要用分号作为分隔符

```shell
command1; command2
```

<br>

# shell 脚本的运行机制

### 退出状态

每个命令执行结束，都会有一个退出状态，可通过 `$?` 访问上一个命令的退出状态

<br>

### 数据流与重定向

<br>

### 字符展开

默认情况下，shell 会将命令以外的字符，一律解析为字符串。只有当遇到特殊符号或关键字，才会改用特定的解析规则

遇到特殊符号时，先解析或计算出表达式的结果，再代入原脚本，这个过程称为展开（Expansion）

<br>

#### 路径名展开

只要单词内包含通配符，则展开为一组文件名

与路径名展开相关的通配符：

- `*` 任意数量的任意字符
- `?` 任意一个字符（不能是零个）
- `[characters]` 字符集中的任意一个字符（不能是零个）
- `[!characters]` 不属于字符集中的任意一个字符（不能是零个）
- `[:class:]` 特定字符集中的任意一个字符（不能是零个）

与路径名展开相关的其他符号：

- `~` 自动展开为家目录路径

<br>

> 如果路径展开失败，表达式将被视为字符串

<br>

#### 算数表达式展开

`$((expression))`

<br>

#### 花括号展开

用于生成多个字符串

- 数字序列：`{1..9}`
- 字母序列：`{a..f}`
- 字符组合：`{1,a,2,b}`

支持嵌套

<br>

#### 变量展开

又称参数展开。叫变量展开更贴切，不过某些“变量”不符合变量命名规则，例如 `$?`，因此叫参数更准确。以下不细究该差异，按需称呼“变量”或“参数”

只要遇到 `$` 符号就进行变量展开

变量分为内置变量（例如：`$PATH`）和自定义变量

查看所有内置变量：`printenv | less`

使用变量的另一种形式是：`${}`，这种形式可以区分变量与字符串拼接时，哪一部分是变量名

```shell
host=xx.com
url=${host}/path
```

位置参数，位次大于 9 时，也需要使用 `${}` 形式，因为 `$10` 会被视为 `$1` 加 `0`

<br>

#### 命令代换

`$(command)`

> 另一种语法是反引号 ``

<br>

#### 避免展开

1、**引号**

引号在 shell 中的一个作用是避免展开

自动展开导致非预期结果的案例：

```shell
echo $100.00 # $1 被当作变量展开
```

双引号：除了 $、\ 和 `，其他特殊符号都保持原状（包括空格）

双引号允许在字符串中实现：变量展开、算数展开、命令替换

单引号：禁止单词分割和所有展开

2、**转义**

要禁止特殊符号展开，可用 \ 转义

<br>

# 语法

### 变量

变量名仅支持数字、字母、下划线，且不能以数字开头

使用**任何未定义的变量，都会返回空**，而不是报错。“空”是指什么都没有，而不是“空字符串”

某些情况下，为了避免参数为空导致报错，需要添加引号

```shell
if [ -e "$file" ]; then ...
```

常量的两种声明方式：

```shell
readonly var
readonly var=value

declare -r var
declare -r varName=value
```

> 赋值语句，变量名、等号和变量值之间不能有空格

<br>

### 函数

一个函数必须至少包含一条命令

函数有两种声明形式：

```shell
a () {
  # command
}

function b () {
  # command
}
```

参数定义在 `$1`、`$2`... 这样的变量中：

```shell
Hello () {
   echo "$1 $2"
}
```

调用函数：

```shell
funct # 无参数调用
funct a b # 传参调用
```

函数的返回值定义在 `$?`：

```shell
Hello () {
  return 10
}

Hello

echo "Return value is $?"
```

以上用法印证：在 shell 中，函数是一种命令

<br>

### 条件

```shell
if commands; then
    commands
else
    commands
fi
```

if 条件判断的本质，是判断**命令执行的退出状态**

```bash
$ type true
true is a shell builtin
$ true
$ echo $?
0                      
$ false
$ echo $?
1                      
```

可见在 shell 中，true 和 false 是一种命令

<br>

#### 测试表达式

有两种写法：

```shell
test expression

[ expression ] # 更流行
```

[] 的好处是可以避免 test 命令的一些问题，例如 test 后跟的大于小于符号不加转义就会被解析成重定向

[] 两侧需加空格，因为按照 shell 的解析方式，不加空格，单词分割后，括号会和单词粘连在一起

```shell
if [a = a]; then echo 1; fi # [a: command not found
```

测试表达式的执行结果是 true 或 false

测试表达式的类型：

- 文件：例如测试文件是否存在
- 字符串：例如判断是否相同
- 整数：例如判断大小

```shell
[ -e file ] # 文件存在
[ -d file ] # 文件存在且为目录 

[ a = a ] # 文本相同
[ a == a ] # 文本相同
[ a != b ] # 文本不同
[ -n a ] # 文本长度大于零

[ a -eq b ] # 两个整数相等
[ a -ne b ] # 两个整数不相等
[ a -gt b ] # a 大于 b
[ a -lt b ] # a 小于  b
[ a -ge b ] # a 大于等于 b
[ a -le b ] # a 小于等于 b

[ a ] # 文本不为 null 或者整数不为 0
```

强化版测试表达式 `[[]]` 增加了两项功能：

```shell
[[ "$INT" =~ ^-?[0-9]+$ ]] # 文本符合正则

[[ $FILE == foo.* ]] # 文件名符合模式
```

同时还支持 &&、|| 操作符。[] 只支持用 -a 和 -o 来表示且和或。所以建议统一用 `[[]]`

专为整数设计的表达式 `(())`：

```shell
int=1

if ((int)); then echo 1; fi # (()) 只处理整数，变量名自然会被替换为变量值，不需要加 $
```

命令、`[[]]` 和 `(())` 都支持 &&、||、!

<br>

#### case 分支

```shell
case word in
  pattern) commands
         ;;
  *) commands
     ;;
esac
```

pattern 和路径名展开中的模式一致

pattern 还支持用 | 相连，表示“或”

```shell
case $val in
  a|A) echo 1
       ;;
esac
```

case 语法不能匹配多个测试条件，新版本的 bash 增加了这个支持

```shell
# 打印 1 2
case a in
            a) echo 1
               # 👇
               ;;&
  [[:alpha:]]) echo 2
               ;;
esac
```

<br>

### 循环

条件判断的原理和 if 一样

支持 continue 和 break

```shell
while commands; do
  commands
done

until commands; do
  commands
done
```

for in 循环

```shell
for variable in words; do
  commands
done
```

words 表示列表，列表就是空格隔开的字符

```shell
for i in a b c; do echo $i; done

for i in {a,c}; do echo $i; done

for i in *.txt; do echo $i; done
```

for 循环

```shell
for (( i=0; i<5; i=i+1 )); do echo $i; done
```

> `(())` 表达式内的空格是任意的

<br>
