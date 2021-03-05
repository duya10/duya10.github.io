# GDB 调试


## 开启core，采集程序崩溃的状态

在 /etc/profile 最后一行添加内容，设置全局开启 croe 文件调试，大小不限，然后立即生效

``` shell
vi /etc/profile

# No core files by default 0, unlimited is oo
ulimit -S -c unlimited > /dev/null 2>&1

source /etc/profile
```

在 /etc/sysctl.conf 添加 core 文件命名规则，变成 core.pid 格式，防止覆盖，并立即启用

``` shell
vi /etc/sysctl.conf

# open, add core.pid 
kernel.core_pattern = ./core_%t_%p_%e
kernel.core_uses_pid = 1

sysctl -p /etc/sysctl.conf
```

core 启用成功，ulimit -c 可以查看是否启用成功，如果为0表示没有开启

![core 设置成功](/assets/images/gdb_cl.png)

## GDB 常用命令

命令 | 意义
-|-
r/run | 开始运行调试
l/list [v] | 列出代码，不带参数则记忆向下滚动10行，v 可以是行号，函数名，当前行号的正负偏移量
n/next [n] | 执行[n 次]下一步代码，会跳过函数
b/break n [if] | 在当前文件的第 n 行设置断点，可选设置条件断点
s/step | 执行下一步代码，遇到函数会进入函数内部
p/print | 输出数据
c/continue | 继续运行程序直到下一个断点
bt/backtrace | 显示调用栈信息
q/quit | 退出 gdb 调试
k/kill | 终止正在调试的程序
info command | info breakpoints 显示当前断点信息，info args 查看当前函数参数值，info locals 查看当前函数栈上值信息，info registers 查看寄存器信息，info threads 显示所有运行的线程信息 ...
d/delete [n] | 删除索引为 n 的断点，若不带参数，则删除所有断点
clear n | 删除第 n 行的断点
多线程调试 | 
info threads | 显示所有运行的线程信息
t/thread id | 多线程调试，切换到指定 id 的线程
set scheduler-locking on/off | 设置多线程单独调试开启/关闭，开启会使其他线程阻塞
多进程调试 | ps -ef 可查找需要调试的进程
attach pid | 将 pid 进程加载进来，加载的进程会阻塞到当前正在运行的地方
info inferiors | 查看正在调试的进程
inferior pid | 切换调试的进程

### 查看内存信息

> (gdb) x /23dw a

从 a 地址开始 23 个 4 字节有符号十进制数输出

通用格式

> (gdb) x /nfu ptr

* x 是 examine 的缩写
* n 表示要显示的内存单元个数
* f 表示显示方法
  * x 按十六进制格式显示
  * d 有符号十进制格式
  * u 无符号十进制格式
  * o 八进制格式
  * t 二进制格式
  * a 十六进制格式
  * i 指令地址格式
  * c 字符格式
  * f 浮点数格式
* u 表示一个地址单元的长度
  * b 表示单字节
  * h 双字节
  * w 四字节
  * g 八字节
* ptr 表示从这个地址开始显示

## 参考链接

* [Linux基础 30分钟GDB调试快速突破](https://www.cnblogs.com/life2refuel/p/5396538.html)
* [gdb调试多进程和多线程命令](https://blog.csdn.net/pbymw8iwm/article/details/7876797)

