Android Hook

Hook 的对象是函数，Hook 的能力包括：

- AOP 编程，before 读/写参数、after 读/写返回值
- replace
- 调用原方法


知识

- 虚拟机运行 java 的机制
- 安卓启动应用的过程
- native 开发技术（linux 平台）
- 动态注入的技术
  - [Hook and Injection (4) (Dynamic Injection)](https://www.programmersought.com/article/60576015375/)


分类

- 静态 hook（硬改）
  - 系统层硬改（定制 ROM、虚拟机）
  - 应用层硬改（重打包、脱机挂）
- 动态 hook
  - 单进程 hook
    - 启动阶段
    - 运行阶段
  - 进程间通信 hook

[盘点 Android 常用 Hook 技术](https://zhuanlan.zhihu.com/p/109157321)
[Hook and Injection(1) (Type of Hook)](https://www.programmersought.com/article/84755060197/)
《Android 软件安全与逆向分析》

硬改的好处是稳定，彻底。问题是更新效率通常较低，复原通常较麻烦。软改避免了这些问题，但运行时动态修改，暴露点较多

分两步看，hook 自己 => hook 别人


Java Hook
- 利用反射（只能 hook Field，不能 hook Method）
    - [几个例子](https://blog.csdn.net/gdutxiaoxu/article/details/81459830)
- 利用 Instrumentation API
- JNI hook
- ClassLoader hook
- Dalvik Hook / ART Hook
  - 利用虚拟机执行 java 代码的机制进行 hook
  - [几种 Dalvik Hook 方案研究](https://blog.csdn.net/wyzzgo/article/details/53706685)

> 简单看，可分为 java 派和 native 派，参考：[ART下的方法内联策略及其对Android热修复方案的影响分析](https://github.com/WeMobileDev/article/blob/master/ART%E4%B8%8B%E7%9A%84%E6%96%B9%E6%B3%95%E5%86%85%E8%81%94%E7%AD%96%E7%95%A5%E5%8F%8A%E5%85%B6%E5%AF%B9Android%E7%83%AD%E4%BF%AE%E5%A4%8D%E6%96%B9%E6%A1%88%E7%9A%84%E5%BD%B1%E5%93%8D%E5%88%86%E6%9E%90.md)
> [YAHFA--ART环境下的Hook框架](http://rk700.github.io/2017/03/30/YAHFA-introduction/)

Binary Hook
- DBI，动态二进制插桩
  - [Pin 动态二进制插桩](https://www.bookstack.cn/read/CTF-All-In-One/doc-5.2.1_pin.md)
- 主要类型
  - 异常 Hook
  - inline hook
  - [EntryPoint hook](https://zhuanlan.zhihu.com/p/59385335)
  - got hook
  - LD_PRELOAD hook
  - 虚表 hook


> [这篇 hook 原理综述文章很重要](https://weishu.me/2017/11/23/dexposed-on-art/)


xposed 检测与对抗

- 静态特征
  - 检测【包名】
  - 检测 xposed 引入的【文件】或【目录】
  - 从 so 中查找【敏感信息】
    - 直接查名称（symbol）、字符串
    - 通过【指令特征】匹配特征函数
- 动态特征
  - 自造异常，从【堆栈】查看有无敏感信息
  - 检测 xposed 引入的【类】
  - 从【/proc/self/maps】检测敏感信息
  - 从【/proc/self/mounts】检测敏感信息
  - 检测方法是否【isNative】
  - 扫描【内存】查找敏感特征

[Android Hook技术防范漫谈](https://tech.meituan.com/2018/02/02/android-anti-hooking.html)
[Xposed注入实现分析及免重启定制](https://bbs.pediy.com/thread-223713.htm)


Frida 检测与对抗

- 静态检测
  - 检测 so 文件段的变化（https://bbs.pediy.com/thread-268586.htm）
- 动态检测
  - 检测进程列表里有无 frida-server（bypass 方法：改名字）
  - 检测 frida-server 默认端口（bypass 方法：改端口）
  - 检测 D-Bus 协议通信（bypass 方法：脱机模式，不依赖 frida-server）
  - 检测 /proc/self/maps

[python反爬之反调试检测frida](https://www.cnblogs.com/Eeyhan/p/13458549.html)
[DetectFrida](https://github.com/darvincisec/DetectFrida)
[strongR-frida-android](https://github.com/hluwa/strongR-frida-android)

hook 框架必要依赖的检测，如 magisk、root，对应方法：hook 检测点，根本方法是去依赖
