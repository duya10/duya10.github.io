# skynet attempt to yield across a C-call boundary 错误问题


## skynet attempt to yield across a C-call boundary 错误问题

### 解决方法

检查出问题的服务是否 require `datasheet/multicast/cluser/sharedata/...`，如果有，则必须在 skynet.start 之前先 require 一下。

### 原因

导致 yield across a C-call boundary 的原因，C(sknet framwork) -> lua(skynet service) -> C -> lua  

最后这个 lua 里如果调用了 yield 就会产生  
这里 require 是一个 C 函数  

skynet.init 就是为了避免你在 require 阶段运行阻塞代码， [https://github.com/cloudwu/skynet/blob/master/lualib/skynet.lua#L588-L600](https://github.com/cloudwu/skynet/blob/master/lualib/skynet.lua#L588-L600) 把它推迟到 skynet.start 再运行  

除非你在别的地方破坏了这个行为，让 [https://github.com/cloudwu/skynet/blob/master/lualib/skynet.lua#L602](https://github.com/cloudwu/skynet/blob/master/lualib/skynet.lua#L602) 不小心运行了。


上面云风的意思就是在 C 函数里调用了 coroutine.yield()。

为什么在类似 require "datasheet" 时会调用到 yield 呢？

``` lua
-- datasheet/init.lua
skynet.init(function()
    datasheet_svr = service.query "datasheet"
end)
```

> skynet.init 的作用  
> 如果你想在 skynet.start 注册的函数之前做点什么，可以调用 skynet.init。这通常用于 lua 库的编写。如果你需要编写的服务在引用你的库的时候，事先调用一些 skynet 阻塞 API，就可以用 skynet.init 把这些工作注册在 start 之前

``` lua
-- skynet.lua
local init_func = {}

function skynet.init(f, name)
    assert(type(f) == "function")
    if init_func == nil then -- 重点
        f()
    else
        table.insert(init_func, f)
        if name then
            assert(type(name) == "string")
            assert(init_func[name] == nil)
            init_func[name] = f
        end
    end
end
```

在调用到 skynet.init 时，如果 init_func 为 nil，就会直接执行 f()，而不是把它放到 init_func 队列里等待执行

而 skynet.init 这个函数是暴露在最外层的，即在 require 时就会被调用，如果 init_func 为 nil，就会直接执行 f()，若 f() 内有 yeild，就会报错。

那么 init_func 什么时候会是 nil 呢？

``` lua
-- skynet.lua
-- 此函数会在 skynet.start 指定的函数调用前被调用
local function init_all()
    print(debug.traceback())
    local funcs = init_func
    init_func = nil -- 在此处被置为空
    if funcs then
        for _, f in ipairs(funcs) do
            f()
        end
    end
end
```

也就是说，在执行 skynet.start 之后，再去 require 上面所说的那些文件，就会调用到 skynet.init，直接执行到其中的函数。

这里的设计思路是：服务已经启动，init_func 的执行时机过去了，于是就直接执行了其中的函数。但是 skynet 中很多模块都在 skynet.init 里做一些查询地址之类的操作，这些操作会调用到 skynet.call，那么就会 yield。这就相当与把 skynet.call 直接暴露在最外面，require 就会直接执行到，那当然会报错。

## 解决方法

既然在 skynet.start 之后不能调用 skynet.init 了，那我们就在 skynet.start 之前调用，也就是把要用到的这类模块（含有这种代码：skynet.init(function() skynet.call(…)end))，统统在服务入口文件的最上方 require 一遍。

只要保证 `require datasheet/cluster/multicast` 在 skynet.start 这行代码之前，就不会有问题了。

