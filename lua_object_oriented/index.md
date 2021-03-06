# Lua 面向对象


## Lua 面向对象

## 隐藏 self 参数

用 lua 进行面向对象的编程
* 声明方法和调用方法统一用 ":"
* 属性的定义和调用全部用   "."

定义的时候冒号默认接收 self 参数，调用的时候默认传递调用者自身作为参数，而点号要显式传递或接收 self 参数。

## 一个简单的类

``` lua
function class(classname, super)
	local superType = type(super)
	local cls
	
	if superType ~= "function" and superType ~= "table" then
		superType = nil
		super = nil
	end
	
	-- inherited(继承) from Lua Object
	if super then -- 复制基类方法
		cls = {}
		setmetatable(cls, {__index = super})
		cls.super = super
	else
		cls = {ctor = function() end} -- 构造函数
	end
	
	cls.__cname = classname
	cls.__index = cls
	
	function cls.new(...) -- 实例化
		local obj_data = {}
		local cls_name = cls.__cname
		
		local instance = setmetatable(obj_data, cls)
		instance.class = cls
		instance:ctor(...)
		return instance
	end
	return cls
end
```

在 lua 中不存在类的定义这样的概念，不管是类的定义还是类的实例都是通过 lua table 来模拟的。实现的 lua 面向对象是 prototype（原型）方式的，即类是一个 lua table，这个 table 定义了类的原型，而类的实例是基于这个原型的另一个 lua table。

实现 lua 面向对象可以分解为类的定义和类的实例化两部分。类的定义主要实现继承，让子类拥有父类的方法集；类的实例化需要解决实例对象如何共享类的方法集，但独享自己的成员变量实例。

子类在定义时复制所有基类的方法，在实例化时将该类作为 metatable 的 __index 赋值给实例。

