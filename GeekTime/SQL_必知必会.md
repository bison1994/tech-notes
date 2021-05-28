# SQL必知必会

SQL必知必会——从入门到数据实战 陈旸


### SQL 是什么

SQL：用于和 DBMS 交互的语言，也是一种开放的标准

MySQL、Oracle 等都是 DBMS

DBMS 最主流的类型是关系型，非关系型的类型有：文档型、键值型、列存储、搜索引擎等

SQL 是**关系型**数据库的查询语言


### SQL 能做什么

- 数据定义（Data Definition Language）：创建，删除和修改数据库和表结构
- 数据查询（Data Query Language）
- 数据操作（Data Manipulation Language）
- 数据控制（Data Control Language）：定义访问权限和安全级别

> 上述只是大致分类，[官方文档](https://dev.mysql.com/doc/refman/8.0/en/sql-statements.html)有详细划分


### MySQL 如何执行

c/s 架构，服务端运行 mysqld 进程。因此使用 MySQL 首先需要启动服务，再建连

mysqld 结构如下：

- 连接层
- sql 层
- 存储引擎层
    - 插件式架构，支持多种存储引擎
    - 每一张表都可以单独指定一种存储引擎

在 sql 层，一条 sql 语句的执行过程如下：

- 缓存查询（MySQL8.0 之后废弃）
- 解析器：语法、语义分析
- 优化器：明确执行路径
- 执行器：权限校验、执行

> sql 语句必须由分号结束


### 数据库操作

- 创建：`CREATE DATABASE [库名];`
- 删除：`DROP DATABASE [库名];`
- 查看：`SHOW DATABASES;`


### 表操作

```sql
CREATE TABLE [表名] (

) ENGINE = [引擎类型] CHARACTER SET = [字符集] COLLATE = [排序规则] ROW_FORMAT = [行格式];
```

字段属性：

- 字段名
- 数据类型
- 是否允许为空
- 默认值
- 唯一性约束
- CHECK 约束
- 自增
- 主键
- 外键
- 索引
- 字符编码
- 排序规则
- 注释


### 数据检索的顺序

关键字的顺序

```sql
SELECT ... DISTINCT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY ... LIMIT
```

语句执行的顺序

```sql
FROM ... WHERE ... GROUP BY ... HAVING ... SELECT ... DISTINCT ... ORDER BY ... LIMIT
```

每个步骤会产生一个虚拟表，传给下一个步骤


