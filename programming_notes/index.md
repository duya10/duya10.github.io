# 编程笔记


## 分布式和集群

分布式：一个业务拆分成多个子业务，部署在不同的服务器上

集群：同一个业务，部署的多个服务器上

## 并发

在讨论并发的时候，如果说 a 早于 b 发生，那么我们就保证了 a 发生的时间一定早于 b，而且是可以预期的。

当 a 既不比 b 早也不比 b 晚，我们说 a 和 b 并发，这并不说明 a 和 b 一定同时发生，只说明我们不能确定它们的顺序。

对于并发我们必须认识到的一点是：关于并发的直觉都不可靠。  

不同的处理器，不同的编译器，都可能产生让你想不到的结果。

### 数据竞态

数据竞态发生在两个线程并发读写同一个变量并且至少其中一个是写入的时候。

避免数据竞态的三种方法：

1. 不要修改变量。从不修改的数据结构和不可变的数据结构本质上是并发安全的，也不需要做任何同步。
2. 避免从多个线程访问同一变量。
3. 允许多个线程访问同一变量，但是同一时间只有一个线程可以访问。这种方法成为**互斥机制**。

## 消息队列

> 消息队列（英语：Message queue）是一种进程间通信或同一进程的不同线程间的通信方式，软件的贮列用来处理一系列的输入，通常是来自用户。消息队列提供了异步的通信协议，每一个贮列中的纪录包含详细说明的数据，包含发生的时间，输入设备的种类，以及特定的输入参数，也就是说：消息的发送者和接收者不需要同时与消息队列互交。消息会保存在队列中，直到接收者取回它。 ——维基百科

1. 异步处理
   * 将耗时较长且不需要即使同步的操作放入消息队列，减少系统响应时间
2. 解耦和
   * 不同服务之间通过消息队列进行通信，只需要定义好消息的格式，服务之间不需要有联系
3. 可靠性
   * 可以通过对消费者的横向扩展，降低消息队列阻塞和单个消费者出现故障的风险
   * 消息队列可以将接收到的消息存储到本地磁盘，这样就算程序或者消息队列挂掉，消息也能被重新加载

加入消息队列会增加系统的复杂度，只有当性能出现瓶颈的时候，可以引入消息队列对可以异步处理的耗时操作进行处理。

应用场景：广播、流量削峰和流量控制、日志处理（Kafka）、消息通讯（聊天室）等等


## 计算机本质

计算机领域最重要的三件事情：存储、计算和传输。

存储大、计算快、传输带宽大延迟低是不可能同时实现的。所以计算机技术一般都是牺牲其中一二，选择更重要的。

存储有关计算机原理，计算有关数据结构和算法，传输有关计算机网络，而三者结合便是计算机操作系统。

数据压缩是牺牲计算换来存储更小，传输更快

缓存对比硬盘是牺牲存储，换来计算更快，传输更少

## 泛型

### 作用

1. 程序更加健壮：编译器在编译期间可以对类型进行检查，避免运行时出现类型转换异常
2. 避免强制转换：不需要显式强制转换类型，代码更加简洁
3. 参数化类型：算法复用，减少代码量

## 边界计算与不对称边界

> 栏杆错误：100 米的距离，每隔 10 米插一根栏杆，需要多少根栏杆？
> 如果不加思索，很可能得出 10 的答案，但是正确的答案应该是 11 根。

要避免“栏杆错误”，有两个通用原则：

1. 首先考虑最简单情况下的特例，然后将得到的结果外推。
2. 仔细计算边界，绝不掉以轻心。

还有一个技巧是采用**左闭右开**的方法，类似 C 语言的数组，a[10] 表示 10 的元素的数组，数组下标 0 到 9，按左闭右开原则表示就是 [0, 10)，
我们很容易得到这个数组的元素个数 `10 - 0 = 10`。

## 参考链接

* [分布式和集群的区别](https://www.zhihu.com/question/20004877)

