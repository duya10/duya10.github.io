# skynet 阅读笔记


## skynet

### skynet 核心层

skynet 的核心层分为 service, socket, timer, monitor 四个模块

#### service

实现了 actor 模型，是最重要的一个模块。其又依赖 handle, mq, module 三个模块。
handle 管理 actor 的生命周期，采用了一种常用的 id 映射和引用计数手法，好处在于可以用 handle 检测对象是否有效。
mq 是消息队列和服务队列的常规实现，用自旋锁保证线程安全。
module 是动态库加载，因为 skynet 约定用动态库来承载服务。
服务的调度是基于多线程的，比例为 m:n，每个服务只能被一个线程持有

#### socket

实现了一个线程安全的 proactor 模式，之所以放在核心层是为了避免调度的开销。

#### timer

时间轮算法的计时器，放在核心层同 socket。

#### monitor

提供了死循环，消息录像，消息处理开销三种监测。

### lua 支持

好处：

1. 动态语言的便捷和安全
2. 用 lua 的协程可以消除异步回调的分隔

skynet 核心是以动态库为承载的，所以要支持 lua 必须要做两件事：

1. 提供一个 c 服务来做 lua 的宿主
2. 将核心层的外部接口封装为 lua 库

snlua 是宿主，lua-skynet.c 是 lua 库

### skynet 的运行流程

1. /skynet-src/skynet_main.c
  加载配置文件，嵌入 lua 代码，是为了方便，在 lua 层解析比用 c api 方便。
2. /skynet-src/skynet_start.c
   初始化子模块，创建工作线程。
3. 创建日志服务，bootstrap 服务。

## 参考

* [skynet GitHub](https://github.com/cloudwu/skynet)  
* [skynet wiki](https://github.com/cloudwu/skynet/wiki)  
* [云风 blog](https://blog.codingnow.com/)

### 记录

* [基于 skynet 的 MMO 服务器设计](https://blog.codingnow.com/2015/04/skynet_mmo.html)

* [IDE 不是程序员的唯一选择（一）](https://blog.codingnow.com/2008/09/replacement_of_ide_1.html)  
* [IDE 不是程序员的唯一选择（二）](https://blog.codingnow.com/2008/09/replacement_of_ide_2.html)  
* [IDE 不是程序员的唯一选择（三）](https://blog.codingnow.com/2008/09/replacement_of_ide_3.html)  
* [IDE 不是程序员的唯一选择（四）](https://blog.codingnow.com/2008/09/replacement_of_ide_4.html)  
* [IDE 不是程序员的唯一选择（五）](https://blog.codingnow.com/2008/10/replacement_of_ide_5.html)  
* [IDE 不是程序员的唯一选择（终）](https://blog.codingnow.com/2008/10/replacement_of_ide_6.html)

* [skynet 框架应用](https://blog.csdn.net/qq769651718/article/category/7480207)  
* [skynet 源码赏析](https://manistein.github.io/blog/post/server/skynet/skynet%E6%BA%90%E7%A0%81%E8%B5%8F%E6%9E%90/)  
* [skynet 服务的本质和缺陷](https://blog.csdn.net/mycwq/article/details/47379277)

