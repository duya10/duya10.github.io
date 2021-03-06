# Lua table


## Lua tabel 与元表

### table

#### 常用函数


``` lua
-- 获取参数表 table，并对这个 table 预定义一个字段 n（表示该表的长度）。
local arg = table.pack(...)
for i = 1, arg.n do  
  print(i, arg[i])
end

--[[
提供一个列表，其所有元素都是字符串或数字，返回字符串 list[i]..sep..list[i+1] ··· sep..list[j]。 
sep 的默认值是空串， i 的默认值是 1 ， j 的默认值是 #list 。 如果 i 比 j 大，返回空串。
]]

table.concat(list [, sep [, i [, j]]])

--[[
在 list 的位置 pos 处插入元素 value ， 并后移元素 list[pos], list[pos+1], ···, list[#list] 。 
pos 的默认值为 #list+1 ， 因此调用 table.insert(t,x) 会将 x 插在列表 t 的末尾。
]]
table.insert(list, [pos,] value)

--[[
将元素从表 a1 移到表 a2。 这个函数做了次等价于后面这个多重赋值的等价操作： a2[t],··· = a1[f],···,a1[e]。 a2 的默认值为 a1。 
目标区间可以和源区间重叠。 索引 f 必须是正数。
]]
table.move(a1, f, e, t [, a2])

--[[
移除 list 中 pos 位置上的元素，并返回这个被移除的值。 
当 pos 是在 1 到 #list 之间的整数时， 它向前移动元素　list[pos+1], list[pos+2], ···, list[#list] 并删除元素 list[#list]； 
索引 pos 可以是 #list + 1 ，或在 #list 为 0 时可以是 0 ； 在这些情况下，函数删除元素 list[pos]。
pos 默认为 #list， 因此调用 table.remove(l) 将移除表 l 的最后一个元素。
]]
table.remove(list [, pos])

--[[
在表内从 list[1] 到 list[#list] 原地 对其间元素按指定次序排序。 
如果提供了 comp ， 它必须是一个可以接收两个列表内元素为参数的函数。 
当第一个元素需要排在第二个元素之前时，返回真 （因此 not comp(list[i+1],list[i]) 在排序结束后将为真）。 
如果没有提供 comp， 将使用标准 Lua 操作 < 作为替代品。
排序算法并不稳定； 即当两个元素次序相等时，它们在排序后的相对位置可能会改变。
]]
table.sort(list [, comp])

--[[
返回列表中的元素。 这个函数等价于
     return list[i], list[i+1], ···, list[j]
i 默认为 1 ，j 默认为 #list。
]]
table.unpack(list [, i [, j]])
```

#### table 的深拷贝

``` lua
--[[
一般表的复制都是引用，深拷贝需要实现：1、值的复制，2、方法的复制
所以要解决 3 个问题：
1、table 存在循环引用
2、metatable（metatable 都不参与复制）
3、keys 也是 table
]]
function deepcopy(obj)
  local Table = {}
  local function func(obj)
    if type(obj) ~= "table" then
      return
    end
    local newtable = {}
    Table[obj] = newtable
    for k, v in pairs(obj) do
      newtable[func(k)] = func(v)
    end
    return setmetatable(newtable, getmetatable(obj))
  end
  return func(obj)
end
```

### 元表

## 参考链接

* [lua table 的内部实现](https://blog.csdn.net/zr339361504/article/details/52432163)

