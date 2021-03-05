# Go 进阶


## 陷阱

### 引用和指针

silce, map, channel 是引用，内部数据结构包含了底层数据结构的指针，所以作为函数参数在函数中改变其中的值能够生效。

其余数据类型在作为函数参数时，由于函数传参是值拷贝，所以如果需要在函数中改变值，就需要传指针。

### 跳出 for-switch 和 fo-select 代码块

没有指定标签的 break 只会跳出 switch/select 语句，若不能使用 return 语句跳出，可以为 break 跳出标签指定的代码块。

*goto* 虽然也能跳转到指定位置，但是会再次进入 for-switch，形成死循环。

``` go
func main() {
loop:
    for {
        switch {
        case true:
            fmt.Println("break")
            break
        }
    }
    fmt.Println("done")
}
```

### defer 函数的参数值

对 defer 延迟执行的函数，它的参数会在声明时候就执行表达式，而不是执行函数的时候才执行。

``` go
func main() {
    var i = 1
    defer fmt.Println("result: ", func() int {return i * 2}())
    i++
}
# result: 2
```

### 类型声明与方法

从一个现有的非 interface 类型创建新类型，不会继承原有的方法；  
将原类型以匿名字段的形式嵌到自定义的新的 struct 中，可以使用原类型的方法；  
使用 interface 类型创建新类型，保留了原类型的方法集。

``` go
type Locker1 sync.Mutex

type Locker2 struct {
    sync.Mutex
}

type Locker3 sync.Locker

func main() {
    var locker Locker1
    locker.Lock() // 报错
    locker.Unlock() // 报错

    var locker Locker2
    locker.Lock() // OK
    locker.Unlock() // OK

    var locker Locker3
    locker.Lock() // OK
    locker.Unlock() // OK
}
```

### 堆栈变量

Go 编译器会根据变量的大小和 "escape analysis" 的结果来决定变量的存储位置，所以可以准确返回本地变量的地址。

在 go build 或 go run 时，加入 -m，能准确分析程序的变量分配的栈堆位置：

> go run -gcflags -m main.go

## 并发和锁

### nil channel

在一个值为 nil 的 channel 上发送和接收数据将永久阻塞，利用这个死锁的特性，可以用在 select 中动态地选择发送或接收的 channel。

### 实现同步

题目描述: 编写一个程序，开启 3 个线程 A,B,C，这三个线程的输出分别为 A、B、C，每个线程将自己的 输出在屏幕上打印 10 遍，要求输出的结果必须按顺序显示。如：ABCABCABC....

关键点：  
1. 开 3 个线程
2. 每个线程将自己的输出打印
3. 每个线程都打印 10 遍

``` go
func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)
    ch3 := make(chan string)
    done := make(chan struct{})

    go func() {
        for {
            print(<-ch1)
            ch2 <- "B"
        }
    }()

    go func() {
        for {
            print(<-ch2)
            ch3 <- "C"
        }
    }()

    go func() {
        for i := 1; i <= 10; i++ {
            print(<-ch3)
            ch1 <- "A"
        }
        done <- struct{}{}
    }()

    ch1 <- "A"

    <-done
}
```

进一步思考：

编写一个程序，开启 N 个线程 A,B,C...，这 N 个线程的输出分别为 A、B、C...，每个线程将自己的输出在屏幕上打印 M 遍，要求输出的结果必须按顺序显示。如：ABC...ABC...ABC...
其中 N <= 1000, M <= 1000

``` go
var (
    N = 3
    M = 10
)

func main() {
    // wait 表示等待信号的管道，signal 表示发送信号的管道
    var wait, signal, firstWait, lastSignal chan struct{}

    wait = make(chan struct{})
    firstWait = wait // 从主 goroutine 发送到第一个

    for i := 0; i < N; i++ {
        signal = make(chan struct{})
        go echo(i, wait, signal)
        wait = signal // 通过这个连接两个 goroutine
    }

    lastSignal = signal // 最后一个把信号发回主 goroutine

    for i := 0; i < M; i++ {
        firstWait <- struct{}{}
        <-lastSignal
    }

    close(firstWait) // 关闭管道，不再接收数据
}

func echo(num int, wait chan struct{}, signal chan struct{}) {
    letter := string('A' + num)

    for _ = range wait {
        fmt.Println(letter)
        signal <- struct{}{}
    }

    close(signal)
}
```

### FanIn

通过将多个输入 channel 多路复用到单个处理 channel 的方式，一个函数能够从多个输入 channel 中读取数据并处理。当所有的输出 channel 都关闭的时候，单个处理 channel 也会关闭。这就叫做扇入。

这里主要将 N 个 channel 的输出输入到一个 channel 中。

``` go
var (
    N = 3
    M = 10
)

func gen(v string, times int) <-chan string {
	ch := make(chan string)
	go func() {
		defer close(ch)
		for i := 0; i < times; i++ {
			ch <- v
		}
	}()
	return ch
}

func fanIn(times int, inputs []<-chan string) <-chan string {
	ch := make(chan string)
	go func() {
		defer close(ch)
		for i := 0; i < times; i++ {
			for _, input := range inputs {
				v := <-input
				ch <- v
			}
		}
	}()
	return ch
}

func main() {
	times := M
	inputs := make([]<-chan string, 0, N)
	for i := 0; i < N; i++ {
		threadName := string('A' + i)
		inputs = append(inputs, gen(threadName, times))
	}
	for char := range fanIn(times, inputs) {
		fmt.Println(char)
	}
}
```

## 断行规则

1. 在 Go 中，注释除外，如果一个代码行的最后一个语法词段（token）为下列所示之一，则分号将自动插入到行尾：
   * 一个标识符
   * 一个整数、浮点数、虚部、码点或者字符串字面表示形式
   * 跳转关键字之一：break、continue、fallthrough 和 return
   * 自增运算符 ++ 或者自减运算符 --
   * 右括号：)、] 或者 }
2. 为了一条复杂语句完全显示在一个代码行中，分号可能被插入在 ')' 或者 '}' 之前


## 参考链接

* [Go 手册](https://golang.google.cn/)
* [Go 手册(中文)](https://go-zh.org/)
* [Go 语言中文社区](https://github.com/Go-zh)
* [Go 指南](https://tour.go-zh.org/list)
* [实效 Go 编程](https://go-zh.org/doc/effective_go.html)
* [Go walker](https://gowalker.org/)
* [Go 夜读](https://github.com/developer-learning/reading-go)
* [Go 夜读](https://reading.developerlearning.cn/)
* [Go's Declaration Syntax, 为什么类型在变量后面](https://blog.go-zh.org/gos-declaration-syntax)
* [Defer, Panic, and Recover](https://blog.go-zh.org/defer-panic-and-recover)
* [Go 切片：用法和本质](https://blog.go-zh.org/go-slices-usage-and-internals#)
* [Go 优缺点](https://studygolang.com/articles/12907)
* [Go coding in go way](https://tonybai.com/2017/04/20/go-coding-in-go-way/)
* [Go: the Good, the Bad and the Ugly](https://bluxte.net/musings/2018/04/10/go-good-bad-ugly/)
* [Golang FAN 模式](https://segmentfault.com/a/1190000017182416)
* [项目部署](https://12factor.net/zh_cn/)

