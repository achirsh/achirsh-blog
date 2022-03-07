---
title: "Go Modules"
date: 2022-03-07
draft: false
summary: "Go"
---

# Go Modules

Go Modules是Go语言中正式官宣的项目依赖解决方案，与Go1.11正式发布，在Go1.14可以用在生产上。

## 什么是Go Modules

Go Modules是Go语言的依赖解决方案，发布于Go1.11，成长于Go1.12，丰富于Go1.13，正式于1.14推荐在生产上使用。

Go Modules目前集成在Go的工具链中，只要安装了Go，自然而然也就可以使用Go Modules了，而Go Modules的出现也解决了在Go1.11前的几个常见争议问题：

```
1、Go语言长久以来的依赖管理问题
2、“淘汰”现有的GOPATH的使用模式
```

## GOPATH的那些点点滴滴

我们有提到Go Modules的解决的问题之一就是“淘汰掉”GOPATH，但是GOPATH又是什么呢。

### GOPATH是什么

我们先看看第一个问题，GOPATH是什么，我们可以输入命令行看看:

```
$ go env
GOPATH="/Users/xxx/Desktop/PROJECT/go"
```
我们输入go env命令行后可以查看到GOPATH变量的结果，我们进入到该目录下进行查看，如下：
```
go
--bin
--pkg
--src
  --github.com
```

GOPATH目录下一共包含了三个字目录，分别是：

```
bin：存储所编译生成的二进制文件。
pkg：存储预编译的目标文件，以加快程序的后续编译速度。
src：存储所有.go文件或源代码。在编写GO应用程序，程序包和库时，一般会以$GOPATH/src/github.com/的路径进行存放。
```
### 为什么启用GOPATH模式

在GOPATH的$GOPATH/src下进行.go文件或源代码的存储，我们可以称其为GOPATH的模式，这个模式，看起来好像没有什么问题，那么为什么我们要启用呢，参见如下原因：

```
GOPATH模式下没有版本控制的概念，具有致命的缺陷，至少会造成以下问题：

  1、在执行go get的时候，你无法传达任何版本信息的期望，也就是说你无法知道自己当前更新的事哪一个版本，也无法通过指定来拉取自己所期望的具体版本。
  2、在运动Go应用程序的时候，你无法保证其它人与你所期望依赖的第三方库时相同的版本，也就是说在项目依赖库的管理上，你无法保证所有人的依赖版本都一致。
  3、你没办法处理v1、v2、v3等等不同版本的引用问题，因为GOPATH模式下的导入路径都是一样的，都是github.com/..

Go语言官方从Go1.11起开始推进Go Modules，Go1.13起不再推荐使用GOPATH的使用模式，Go Modules也渐趋稳定，因为新项目也没有必要继续使用GOPATH模式。
```

## Go Modules基本使用

在初步了解了Go Modules的前世今生后，我们正式进入到Go Modules的使用，首先我们将从头开始创建一个Go Modules的项目（原则上所创建的目录应该不要放在GOPATH之中）

### 所提供的命令

在Go Modules中，我们能够使用如下命令进行操作：

命令 | 作用
---|---
go mod init | 生成go.mod文件
go mod download | 下载go.mod文件中指明的所以依赖
go mod tidy | 整理现有的依赖
go mod graph | 查看现有的依赖结构
go mod edit | 编辑go.mod文件
go mod vendor | 导出项目所有的依赖到vendor目录
go mod verify | 检验一个模块是否被篡改过
go mod why | 查看为什么需要依赖某模块

### 所提供的环境变量

在Go Modules中有如下常用环境变量，我们可以通过go env命令来查看

```
$ go env
GO111MODULE="auto"
GOPROXY="https://proxy.golang.org,direct"
GONOPROXY=""
GOSUMDB="sum.golang.org"
GONOSUMDB=""
```

### GO111MODULE

Go语言提供了GO111MODULE这个环境变量来作为Go Modules的开关，其允许设置以下参数：
```
auto：只要项目包含了go.mod文件的话启用Go Modules，目前在Go1.11至Go1.14中仍然是默认值。
on：启用Go Modules，推荐设置，将会是未来版本中的默认值。
off：禁用Go Modules，不推荐设置。
```

### GOPROXY

这个环境变量主要是用于设置Go模块代理（Go module proxy），其作用是用于使Go在后续拉取模块版本时能够脱离传统的VCS方式，直接通过镜像站点来快速拉取。

GOPROXY默认值是：https://proxy.golang.org,direct，这有一个很严重的问题，就是proxy.golang.org在国内是无法访问的，因此这会直接卡住你的第一步，所以你必须开启Go modules的同时设置国内的Go模块代理，执行如下命令：
```
$ go env -w GOPROXY=https://goproxy.cn,direct
```
GOPROXY 的值是一个以英文逗号 “,” 分割的 Go 模块代理列表，允许设置多个模块代理，假设你不想使用，也可以将其设置为 “off” ，这将会禁止 Go 在后续操作中使用任何 Go 模块代理。

#### direct是什么

而在刚刚设置的值中，我们可以发现值列表中有 “direct” 标识，它又有什么作用呢？

实际上 “direct” 是一个特殊指示符，用于指示 Go 回源到模块版本的源地址去抓取（比如 GitHub 等），场景如下：当值列表中上一个 Go 模块代理返回 404 或 410 错误时，Go 自动尝试列表中的下一个，遇见 “direct” 时回源，也就是回到源地址去抓取，而遇见 EOF 时终止并抛出类似 “invalid version: unknown revision...” 的错误。