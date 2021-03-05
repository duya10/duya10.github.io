# skynet 缺陷和隐患


## 内存问题

skynet 服务在处理新消息时，是通过创建新协程来处理的（见co_create），虽然协程会被重复利用，但在当前版本下，这种不断创建协程来消息的方式本身存在不稳定因素：
1. 协程只增加不减少，意味过了某个并发高峰后内存不会降下来。
2. 创建协程也有一定开销，容易触发GC，也占用内存，协程的数量规模不容易控制
3. 如果解决第1点，最槽糕的情况是，不断要创建协程，不断要销毁协程，频繁触发gc

## 同步问题

当一个服务 call 其他服务时，当前协程会挂起，但是当前服务还可以继续接受并处理其他消息。如果中间有多个协程更改同一个数据的话，不做同步处理是无法确定这个数据的。

解决方法：加多一个 state 的标识和一个协程列表，操作执行时，将 state 置为 true，其他协程判断 state == true 时将自己加到协程列表中，然后 skynet.wait。在操作执行完后，重置 state，然后遍历协程列表依次 skynet.wakeup(co)，最后将协程列表清空。

``` lua
local state = nil
local wait_co_list = {}
if state == nil then
    state = true
    do something yield
    state = nil

    if next(wait_co_list) then
        for i, co in ipairs(wait_co_list) do
            skynet.waitup(co)
        end
        wait_co_list = {}
    end
else
    local co = corouine.running()
    table.insert(wait_co_list, co)
    skynet.wait(co)
end
```

