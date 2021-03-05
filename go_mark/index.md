# Go 笔记


## 包

约定包名与导入路径的最后一个元素相同
> math/rand 的包名为 rand

## 函数

函数参数不是指针的话，即为值传递。

多个函数的已命名形参类型相同时，除最后一个类型外，其他的都可以省略
> x int, y int  
> x, y int

### 函数的闭包

Go 函数可以是一个闭包。闭包是一个函数值，它引用了其函数体之外的变量。该函数可以访问并赋予其引用的变量的值，换句话说，该函数被“绑定”在了这些变量上。闭包保证了函数内引用变量的生命周期和函数的活动时间相同。

``` go
func adder() func(int) int {  
    sum := 0 -- 函数外部的变量  
    return func(x int) int { -- 闭包  
        sum += x  
        return sum  
    }  
}
```

## 变量

### 短变量声明

在函数中，简洁赋值语句 := 可在类型明确的地方代替 var 声明。

函数外的每个语句都必须以关键字开始（var, const, func 等等），因此 := 结构不能在函数外使用。

## 基本类型

* bool
* string
* int int8 int16 int32 int64
* uint uint8 uint16 uint32 uint64
* byte // uint8 的别名
* rune // int32 的别名，表示一个 Unicode 码点
* float32 float64
* complex64 complex128 // 复数类型：20+5i

> int, uint 和 uintptr 在 32 位系统上通常为 32 位宽，在 64 位系统上则为 64 位宽。 当你需要一个整数值时应使用 int 类型，除非你有特殊的理由使用固定大小或无符号的整数类型。

## 零值

没有明确初始值的变量声明会被赋予它们的 **零值**。

* 数值类型零值为 0
* 布尔类型零值为 false
* 字符串的零值为 ""(空字符串)

## 常量

> const Pi = 3.141592653589793

常量的声明与变量类似，只不过是使用 const 关键字。

常量可以是字符、字符串、布尔值或数值。

常量不能用 := 语法声明。

## defer

defer 语句会将函数推迟到外层函数返回之后执行。

推迟调用的函数其参数会立即求值，但直到外层函数返回前该函数都不会被调用。

推迟的函数调用会被压入一个栈中。当外层函数返回时，被推迟的函数会按照后进先出的顺序调用。

## 指针

类型 *T 是指向 T 类型值的指针。其零值为 nil。
> var p *int

& 操作符会生成一个指向其操作数的指针。
> i := 42  
> p = &i  

\* 操作符表示指针指向的底层值。
> fmt.Println(*p) // 通过指针 p 读取 i  
> *p = 21         // 通过指针 p 设置 i

## 结构体

一个结构体（struct）就是一个字段的集合。
``` go
type Vertex struct {  
    X int  
    Y int  
}
```

## 数组

类型 [n]T 表示拥有 n 个 T 类型的值的数组。数组的长度是其类型的一部分，因此数组不能改变大小。
> var a [10]int

## 切片 slice

类型 []T 表示一个元素类型为 T 的切片。

切片通过两个下标来界定，即一个上界和一个下界，二者以冒号分隔：  
上标和下标均可省略，上标缺省值为 0，下标缺省值为 a 的长度
> a[low : high]

它会选择一个半开区间，包括第一个元素，但排除最后一个元素。

切片并不存储任何数据，它只是描述了底层数组中的一段。

更改切片的元素会修改其底层数组中对应的元素。

与它共享底层数组的切片都会观测到这些修改。

### 零值

切片的零值是 nil。

nil 切片的长度和容量为 0 且没有底层数组。

### 长度和容量

切片的长度就是它所包含的元素个数。

切片的容量是从它的第一个元素开始数，到其底层数组元素末尾的个数。

切片 s 的长度和容量可通过表达式 len(s) 和 cap(s) 来获取。

### 用 make 创建切片

切片可以用内建函数 make 来创建，这也是你创建动态数组的方式。

make 函数会分配一个元素为零值的数组并返回一个引用了它的切片：
> a := make([]int, 5)  // len(a)=5

要指定它的容量，需向 make 传入第三个参数：
>b := make([]int, 0, 5) // len(b)=0, cap(b)=5

### 向切片追加元素

> func append(s []T, vs ...T) []T

append 的第一个参数 s 是一个元素类型为 T 的切片，其余类型为 T 的值将会追加到该切片的末尾。

append 的结果是一个包含原切片所有元素加上新添加元素的切片。

当 s 的底层数组太小，不足以容纳所有给定的值时，它就会分配一个更大的数组。返回的切片会指向这个新分配的数组。

### 映射 map

映射将键映射到值。

映射的零值为 nil 。nil 映射既没有键，也不能添加键。

make 函数会返回给定类型的映射，并将其初始化备用。
> m = make(map[string]int)

### 修改映射

在映射 m 中插入或修改元素：
> m[key] = elem

获取元素：
> elem = m[key]

删除元素：
> delete(m, key)

通过双赋值检测某个键是否存在：
> elem, ok = m[key]

若 key 在 m 中，ok 为 true ；否则，ok 为 false。

若 key 不在映射中，那么 elem 是该映射元素类型的零值。

同样的，当从映射中读取某个不存在的键时，结果是映射的元素类型的零值。

注 ：若 elem 或 ok 还未声明，你可以使用短变量声明：
> elem, ok := m[key]

## 方法

> 为结构体类型实现方法即为类。

> 方法只是个带接收者参数的函数。

方法就是一类带特殊的 **接收者** 参数的函数。

方法接收者在它自己的参数列表内，位于 func 关键字和方法名之间。

接收者的类型定义和方法声明必须在同一包内；不能为其它包内定义的类型的接收者声明方法；也不能为内建类型（包括 int 之类的）声明方法。

可以为指针接收者声明方法。

这意味着对于某类型 T，接收者的类型可以用 *T 的文法。（此外，T 不能是像 *int 这样的指针。）

``` go
type MyFloat float64

func (f MyFloat) Abs() float64 { // 方法
    ...
}

type Vertex struct {
    X, Y float64
}

func (v Vertex) Abs() float64 { // 为值接收者声明方法
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func (v *Vertex) Scale(f float64) { // 为指针接收者声明方法
    v.X = v.X * f
    v.Y = v.Y * f
}

func main() {
    f := MyFloat(-math.Sqrt2)
    f.Abs() // 调用方法
}
```
### 方法与指针重定向

带指针参数的函数必须接受一个指针；接受一个值作为参数的函数必须接受一个指定类型的值。

以指针为接收者的方法被调用时，接收者既能为值又能为指针；以值为接收者的方法被调用时，接收者既能为值又能为指针。

``` go
type Vertex struct {
    X, Y float64
}

func (v *Vertex) Scale(f float64) { // 指针为接收者的方法
    v.X = v.X * f
    v.Y = v.Y * f
}

func ScaleFunc(v *Vertex, f float64) { // 带指针参数的函数
    v.X = v.X * f
    v.Y = v.Y * f
}

func (v Vertex) Abs() float64 { // 以值为接收者的方法
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func AbsFunc(v Vertex) float64 { // 以值为参数的函数
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
    v := Vertex{3, 4}
    v.Scale(2) // OK
    (&v).Scale(2) // OK
    ScaleFunc(v, 10) // 编译错误
    ScaleFunc(&v, 10) // OK

    v.Abs() // OK
    (&v).Abs() // OK
    AbsFunc(v) // OK
    AbsFunc(&v) // 编译错误

    fmt.Println(v, p)
}
```

### 选择值或指针作为接收者

使用指针接收者的原因有二：

1. 方法能够修改其接收者指向的值。

2. 这样可以避免在每次调用方法时复制该值。若值的类型为大型结构体时，这样做会更加高效。

## 接口

**接口类型**是由一组方法签名定义的集合。

接口类型的变量可以保存任意实现了这些方法的值。

``` go
type I interface {
    Abs() float64
}

// 类型 F 实现了接口 I
type F float64
func (f MyFloat) Abs() float64 {
    if f < 0 {
        return float64(-f)
    }
    return float64(f)
}

// 类型 *T 实现了接口 I
type V struct {
    x, y float64
}
func (v *V) Abs() float64 {
    return math.Sqrt(v.x * v.x + v.y * v.y)
}

func main() {
    var i I 
    f := F(math.Sqrt2)
    v := V{3, 4}

    a = f // OK
    a = &v // OK

    a = v // 编译错误，Vertex 没有实现接口 I

    var a I = V{3, 4} // OK
}
```

### 接口值

接口也是值。它们可以像其它值一样传递。接口值可以用作函数的参数或返回值。

在内部，接口值可以看做包含值和具体类型的元组：
> (value, type)

接口值保存了一个具体底层类型的具体值。

接口值调用方法时会执行其底层类型的同名方法。

``` go
func main {
    var i I = F(-1)

    fmt.Printf("%v, %T", i, i) // -1, main.F
}
```

### 底层值为 nil 的接口值和 nil 接口值

即便接口内的具体值为 nil，方法仍然会被 nil 接收者调用。

注意: 保存了 nil 具体值的接口其自身并不为 nil。

nil 接口值既不保存值也不保存具体类型。

为 nil 接口调用方法会产生运行时错误，因为接口的元组内并未包含能够指明该调用哪个 具体 方法的类型。

``` go
type I interface {
    M()
}

type T struct {

}

func (t *T) M() {

}

func main() {
    var i I
    fmt.Printf("(%v, %T)\n", i, i) // <nil>, <nil>
    i.M() // 编译错误

    var t *T
    i = t
    fmt.Printf("(%v, %T)\n", i, i) // <nil>, *main.T
    i.M() // OK
}
```

###  空接口

指定了零个方法的接口值被称为 *空接口*：
> interface{}

空接口可保存任何类型的值。（因为每个类型都至少实现了零个方法。）

空接口被用来处理未知类型的值。例如，fmt.Print 可接受类型为 interface{} 的任意数量的参数。

### 类型断言

**类型断言**提供了访问接口值底层具体值的方式。
> t := i.(T)

该语句断言接口值 i 保存了具体类型 T，并将其底层类型为 T 的值赋予变量 t。

若 i 并未保存 T 类型的值，该语句就会触发一个 panic。

为了 判断 一个接口值是否保存了一个特定的类型，类型断言可返回两个值：其底层值以及一个报告断言是否成功的布尔值。
> t, ok := i.(T)

若 i 保存了一个 T，那么 t 将会是其底层值，而 ok 为 true。

否则，ok 将为 false 而 t 将为 T 类型的零值，程序并不会产生 panic。

注意：这种语法和读取一个 map 映射时的相同之处。

### 类型选择

**类型选择**是一种按顺序从几个类型断言中选择分支的结构。

类型选择与一般的 switch 语句相似，不过类型选择中的 case 为类型（而非值）， 它们针对给定接口值所存储的值的类型进行比较。

``` go
switch v := i.(type) {
case T:
    // v 的类型为 T
case S:
    // v 的类型为 S
default:
    // 没有匹配，v 与 i 的类型相同, 即一个包含值和具体类型的接口值
}
```

类型选择中的声明与类型断言 i.(T) 的语法相同，只是具体类型 T 被替换成了关键字 type。

此选择语句判断接口值 i 保存的值类型是 T 还是 S。在 T 或 S 的情况下，变量 v 会分别按 T 或 S 类型保存 i 拥有的值。在默认（即没有匹配）的情况下，变量 v 与 i 的接口类型和值相同。

## 并发

