# Go mod 和 goproxy


## go module

*Go 1.11* 版本发布之后，官方引入了 *go module* 来解决依赖管理的问题，*go module* 被集成到原生的 *go cmd* 中。

[go modules wiki](https://github.com/golang/go/wiki/Modules)

[使用 Go Module](https://lingchao.xin/post/using-go-modules.html)

[深入浅出Go Modules](https://blog.caojun.xyz/posts/gomodules/)

## goproxy

*Go 1.00* 版本还新增了 *GOPROXY* 环境变量。如果设置了该变量，下载源代码时将会通过这个环境变量设置的代理地址，而不再是以前的直接从代码库下载。我们可以通过这个解决之前获取 *go get golang.org/x* 包失败的问题。

[*goproxy.io*](https://github.com/goproxyio/goproxy) 这个开源项目帮我们实现好了我们想要的。该项目允许开发者一键构建自己的 *GOPROXY* 代理服务。同时，也提供了公用的代理服务 *https://goproxy.io*，我们只需设置该环境变量即可正常下载被墙的源码包了：

> export GOPROXY=https://goproxy.io

通过置空这个环境变量来关闭:

> export GOPROXY=


新发现的访问速度更快的 GOPROXY 地址

> export GOPROXY=https://goproxy.cn

