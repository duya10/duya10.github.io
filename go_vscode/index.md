# VSCode 配置 Go 环境


## 安装

Go 语言支持以下系统：

* Linux
* FreeBSD
* Mac OS X（也称为 Darwin）
* Windows

[安装下载地址](https://golang.google.cn/dl/)

| 操作系统 | 包名
| :-: | :-: |
| Windows | go1.x.windows-amd64.msi
| Linux | go1.x.linux-amd64.tar.gz
| Mac | go1.x.darwin-amd64-osx10.8.pkg
| FreeBSD | go1.x.freebsd-amd64.tar.gz

![go install](/images/go_download.jpg)

### UNIX/Linux/Mac OS X, 和 FreeBSD 安装

1. 下载二进制包：go1.4.linux-amd64.tar.gz。
2. 将下载的二进制包解压至 /usr/local目录。
   > tar -C /usr/local -xzf go1.4.linux-amd64.tar.gz
3. 将 /usr/local/go/bin 目录添加至PATH环境变量
   > export PATH=$PATH:/usr/local/go/bin

### Windows 系统下安装

Windows 下可以使用 .msi 后缀(在下载列表中可以找到该文件，如go1.4.2.windows-amd64.msi)的安装包来安装。

默认情况下.msi文件会安装在 c:\Go 目录下。你可以将 c:\Go\bin 目录添加到 PATH 环境变量中。添加后你需要重启命令窗口才能生效。

### 安装测试

创建工作目录 C:\>Go_WorkSpace。

文件名: test.go，代码如下：

``` go
    package main

    import "fmt"

    func main() {
        fmt.Println("Hello World!")
    }
```

使用 go 命令执行以上代码输出结果如下：

> C:\Go_WorkSpace>go run test.go  
> Hello, World!

## VSCode 配置 go

下载插件 **Go**

![go](/images/go_vscode_plugin.png)

插件特性包括：

* Colorization 代码着彩色
* Completion Lists 代码自动完成（使用gocode）
* Snippets 代码片段
* Quick Info 快速提示信息（使用godef）
* Goto Definition 跳转到定义（使用godef）
* Find References 搜索参考引用（使用go-find-references）
* File outline 文件大纲（使用go-outline）
* Workspace symbol search 工作区符号搜索（使用 go-symbols）
* Rename 重命名（使用gorename）
* Build-on-save 保存构建（使用go build和go test）
* Format 代码格式化（使用goreturns或goimports或gofmt）
* Add Imports 添加引用（使用 gopkgs）
* Debugging 调试代码（使用delve）

### 安装插件

> Ctrl+Shift+p 打开控制面板
> Go:Install/Update Tools

这时候一般会出现安装报错，只有部分插件能够安装成功

### 报错原因

在安装go插件时，会自动更新很多依赖库文件，都是从Github更新下来，但是因为Github的文件中，多有应用go官网中的文件，因为网络缘故，不能直接下载，导致安装失败。

### 解决方法

使用git软件的Git Bash Here 命令行窗口进行（要求使用，否则git clone时无法下载插件），进入安装go时创建的GOPATH目录（“此电脑->属性->高级系统设置->高级->环境变量->系统变量”中的GOPATH目录）：

#### 创建github.com插件目录及下载插件

> cd $GOPATH/src  
> mkdir github.com  
> cd $GOPATH/src/github.com  
> mkdir acroca cweill derekparker go-delve josharian karrick mdempsky pkg ramya-rao-a rogpeppe sqs uudashr  
> cd $GOPATH/src/github.com/acroca  
> git clone https://github.com/acroca/go-symbols.git  
> cd $GOPATH/src/github.com/cweill  
> git clone https://github.com/cweill/gotests.git  
> cd $GOPATH/src/github.com/derekparker  
> git clone https://github.com/derekparker/delve.git  
> cd $GOPATH/src/github.com/go-delve  
> git clone https://github.com/go-delve\delve.git  
> cd $GOPATH/src/github.com/josharian  
> git clone https://github.com/josharian/impl.git  
> cd $GOPATH/src/github.com/karrick  
> git clone https://github.com/karrick/godirwalk.git  
> cd $GOPATH/src/github.com/mdempsky  
> git clone https://github.com/mdempsky/gocode.git  
> cd $GOPATH/src/github.com/pkg  
> git clone https://github.com/pkg/errors.git  
> cd $GOPATH/src/github.com/ramya-rao-a  
> git clone https://github.com/ramya-rao-a/go-outline.git  
> cd $GOPATH/src/github.com/rogpeppe  
> git clone https://github.com/rogpeppe/godef.git  
> cd $GOPATH/src/github.com/sqs  
> git clone https://github.com/sqs/goreturns.git  
> cd $GOPATH/src/github.com/uudashr  
> git clone https://github.com/uudashr/gopkgs.git  

#### 创建golang.org插件目录及下载插件

> cd $GOPATH/src  
> mkdir -p golang.org/x  
> cd golang.org/x  
> git clone https://github.com/golang/tools.git  
> git clone https://github.com/golang/lint.git  

#### 手动安装插件

> cd $GOPATH/src  
> go install github.com/mdempsky/gocode  
> go install github.com/uudashr/gopkgs/cmd/gopkgs  
> go install github.com/ramya-rao-a/go-outline  
> go install github.com/acroca/go-symbols  
> go install github.com/rogpeppe/godef  
> go install github.com/sqs/goreturns  
> go install github.com/derekparker/delve/cmd/dlv  
> go install github.com/cweill/gotests  
> go install github.com/josharian/impl  
> go install golang.org/x/tools/cmd/guru  
> go install golang.org/x/tools/cmd/gorename  
> go install golang.org/x/lint/golint  

至此，插件安装成功，重启 VSCode 即可编写 Go。

## 后续更新，更好的解决方案，一键解决 go get golang.org/x 包失败

[go mod 和 goproxy](/gomod_goproxy/)

