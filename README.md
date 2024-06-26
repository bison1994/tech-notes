# Web 软件工程师知识架构

### 一、通用专业知识

- 计算机组成：CPU、主存、I/O、总线
- 操作系统
    - 任务管理
        - 任务抽象：进程、线程
        - 任务调度
        - 任务通信
        - 任务并发
    - 内存管理
        - 物理内存
        - 虚拟内存
    - I/O 与文件：文件系统、硬盘（Disk，双向 I/O 设备）、输入/输出设备管理
- 计算机网络
    - 网络架构：分层模型、网络协议
    - 网络安全
        - 应对窃取与篡改：密码学基础、鉴别技术（数字签名、MD5/SHA...）
        - 应对恶意程序：XSS、CSRF、SQL Inject...
        - 应对 DoS
- 数据结构：各类数据结构及其在增删查中的优劣及原理
    - 线性结构：字符串、数组/集、队列、栈、链表、字典
    - 非线形结构：树/堆、图
- 算法：
    - 操作：排序、查找、遍历
    - 思想：分治、贪心、动态规划...


### 二、通用专业技能

- 编程语言
    - 语法与库 API
    - 模块化
    - 编译
    - 运行时
- 编程方法论
    - 评价维度：兼容性、耦合性、健壮性、可扩展性、可复用性、复杂度...
    - 设计原则：SOLID 及其他
        - 单一职责（SRP，Single Responsibility Principle）
        - 开闭原则（OCP，Open-Closed Principle）：内聚/封装 + 可扩展
        - 里氏替换（LSP，Liskov Substitution Principle）
        - 接口隔离（ISL，Interface Segregation Principle）
        - 依赖倒置（DIP，Dependency Inversion Principle）：具体的继承抽象的，高层的依赖底层的，不要倒置
        - 其他：迪米特法则、好莱坞原则...
    - 设计模式（[Design Pattern](https://refactoringguru.cn/design-patterns/catalog)）
        - 创建型
        - 结构型
        - 行为型
    - 架构模式：MVC、MVVM、代理模式、生产者/消费者模式...
    - 编程范式：面向对象（OOP）、面向过程（POP）、面向切面（AOP）、函数式（FP）
    - 规约/规范/最佳实践
- 软件开发模式/软件工程方法/项目管理方法：
    - 传统模式（生命周期、瀑布模型、原型模型、螺旋模型、增量模型...）
    - 敏捷模式（XP 极限编程、Scrum、测试驱动 TDD、领域驱动 DDD...）
- 理论与定律
    - 分布式理论：CAP、BASE
    - 事务理论：ACID
    - 康威定律
    - ...


### 三、后端/服务端

- 开发
    - 数据库：MySql...
    - 框架 Framework：Spring（Spring Boot）、Spring MVC...
    - 中间件 Middleware（桥接客户端和数据资源的通用组件）
        - ORM：MyBatis...
        - 缓存：Redis、Memcached...
        - 消息队列（messaging and streaming broker）：Kafka、RabbitMQ、RocketMQ...
        - RPC：Netty、Dubbo、Thrift...
        - 服务器 web server（Host static/dynamic HTTP request）：Nginx、Tomcat、Jetty...
        - 负载均衡 Load Balance
        - 代理 Proxy
        - 认证：JWT、SSO、Oauth...
        - 日志
        - 网关
        - 配置中心
        - 搜索
        - 权限
    - 存储：hive...
    - 分布式
        - 微服务/容器化：Docker、Kubernetes
        - zookeeper
- 运维
    - 性能优化
    - 质量保障
        - 事前测试：单测、压测、灰度、故障演练...
        - 事中监控
        - 事后诊断
    - 可用性保障
    - 工程化
        - 依赖管理：Maven、Gradle
        - 版本管理：Git
        - 持续交付
- 架构
    - 


### 四、前端/客户端

- 



> https://pdai.tech/

> https://github.com/xingshaocheng/architect-awesome/blob/master/README.md

> https://coolshell.me/articles/skill-map-for-backend-engineer.html
