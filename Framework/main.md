### JavaEE 框架/架构

- JavaEE（Java Platform Enterprise Edition）= JavaSE + 一大堆服务器相关的库以及 API
    - JavaSE（Java Platform Standard Edition）
    - JavaEE 最核心的组件就是基于 Servlet 标准的 Web 服务器，开发者编写的应用程序是基于Servlet API并运行在Web服务器内部的
    - JavaEE 其他代表性技术标准/API
        - EJB（Enterprise JavaBean）：早期经常用于实现应用程序的业务逻辑，现在基本被轻量级框架如 Spring 所取代
        - ...
- JavaEE 流行架构
    - SSH：Structs + Spring + Hibernate（2015 年之前的远古架构）
    - SSM：Spring + Spring MVC + MyBatis
- Spring
- Spring Boot：可认为是 Spring 的脚手架，主要解决的问题是基于“约定大于配置”的原则预置了大多数配置，并且将手动用 xml 配 bean 的操作自动化了，同时内置了 tomcat 服务器，从而实现了 Spring 开箱即用
    - 因为我们编写的 Servlet 程序并不是直接运行的，而是由 Web 服务器加载后创建实例运行，所以，类似 Tomcat、Jetty 这样的 Web 服务器也称为 **Servlet 容器**