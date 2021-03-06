# Lua API


## gcc 命令

编译使用 lua api 的 c 语言程序

> gcc temp.c -o temp -llua -lm -ldl

生成动态链接库供 lua 程序调用

> gcc temp.c -o temp.so -fPIV -shared

## LUA_API

定义
``` c
/*
@@ LUA_API is a mark for all core API functions.
@@ LUALIB_API is a mark for all auxiliary library functions.
@@ LUAMOD_API is a mark for all standard library opening functions.
** CHANGE them if you need to define those functions in some special way.
** For instance, if you want to create one Windows DLL with the core and
** the libraries, you may want to use the following definition (define
** LUA_BUILD_AS_DLL to get it).
*/
#if defined(LUA_BUILD_AS_DLL)	/* { */

#if defined(LUA_CORE) || defined(LUA_LIB)	/* { */
#define LUA_API __declspec(dllexport)
#else						/* }{ */
#define LUA_API __declspec(dllimport)
#endif						/* } */

#else				/* }{ */

#define LUA_API		extern

#endif				/* } */


/* more often than not the libs go together with the core */
#define LUALIB_API	LUA_API
#define LUAMOD_API	LUALIB_API
```

函数开头有 **LUA_API**:

* LUA_API : 标识核心 API 函数
* LUALIB_API : 标识辅助库函数
* LUAMOD_API : 标识标准库函数
* 有特殊需求可以自行修改。默认的，LUA_API、LUALIB_API、LUAMOD_API 是一样的

## lua_lock 和 lua_unlock

定义
``` c
/*
** macros that are executed whenever program enters the Lua core
** ('lua_lock') and leaves the core ('lua_unlock')
*/
#if !defined(lua_lock)
#define lua_lock(L)	((void) 0)
#define lua_unlock(L)	((void) 0)
#endif
```

> If you port Lua to another platform, you are "allowed" to overwrite lua_lock with your own definition; and this definition should essentially be a mutex, to disallow cross-thread operations on the same Lua objects. Essentially, when implemented, it should act similarly to Python's Global Interpreter Lock (GIL).

> 当需要将 Lua 移植到其他平台时，可以重写 lua_lock。必须注意的是，为了避免线程间对同一 Lua 对象的操作，lua_lock 的定义必须是互斥的，且其实现中其行为应该和 Python 的全局解释器锁（GIL）类似。

lua_lock 和 lua_unlock 主要用于线程间通信的情况，一般情况下我们不需要考虑，Lua 官方也对其做了保留，如果有需要涉及到多线程操作，则需开发者自行实现互斥行为。

## 参考链接

* [_Reyn_ : lua 专题](https://www.jianshu.com/c/be33befa3ed2)
* [yuanlin2008 : 探索Lua5.2内部实现](https://blog.csdn.net/yuanlin2008/article/category/1307277)

