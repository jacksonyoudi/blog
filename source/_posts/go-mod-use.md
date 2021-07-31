---
title: go mod使用
date: 2021-07-31 15:45:52
img: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/PuXsa5.jpg
top: true
hide: false
cover: true
coverImg: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/YDsDmh.jpg
toc: true
mathjax: false
summary: 本文很细致的介绍go mod使用，一步一步代码完全掌握go mod，深入学习
categories: go
tags:
-  go
- tool
- language


---


最近由于换工作，开始交接工作。整理以前的工作内容，由于组内就我一个在做go和大数据。 所以开发没有规划，当时是怎么快怎么来。go也是使用最传统的go path的方式管理的。都是手动管理依赖的。现在交接给他人，需要多人开发，发现很多问题。比如版本问题，各种依赖的问题等等。

由于工作原因，几乎所有主流语言都写过。所以，对应语言包管理工具也都了解和使用过。我前面有写过maven的使用。
maven是使用过的功能最强大的包管理工具了，maven定位是项目管理工具。pip和npm都是及格的产品。

我个人觉得，一个包管理工具应该有以下功能：

### 基本功能
1. 依赖管理
2. 依赖包版本控制
3. 对应的包管理平台
4. 可以私有化部署

加分：
5. 代码包是否可以复用
6. 构建，测试,打包
7. 发布上线


对比上面几点：
目前做的最好的也就 maven了，gradle没有使用过，不知道。


今天主角是go mod，先来谈谈没有使用go mod之前的问题。

### 使用go path问题
1. 代码开发必须在go path src目录下，不然，就有问题。
2. 依赖手动管理
3. 依赖包没有版本可言

从这个看， go path不算包管理工具

### govendor
1. 解决了包依赖，一个配置文件就管理
2. 依赖包全都下载到项目vendor下，每个项目都把有一份。拉取项目时,开始怀疑人生。




### go mod介绍
go modules 是 golang 1.11 新加的特性。现在1.12 已经发布了，是时候用起来了。Modules官方定义为：
> 模块是相关Go包的集合。modules是源代码交换和版本控制的单元。 go命令直接支持使用modules，包括记录和解析对其他模块的依赖性。modules替换旧的基于GOPATH的方法来指定在给定构建中使用哪些源文件。


### 如何使用go mod
首先，必须升级go到1.11,目前版本是1.14
下面我以我自己升级演示：

```bash

### 卸载旧版本，删除对应文件
brew uninstall -f go

### 更新一下brew

brew update


### 安装go
brew install go
```

上面升级完了，使用 `go version`看下版本
```
go version go1.14.1 darwin/amd64
```

下面设置go mod和go proxy

```bash
go env -w GOBIN=/Users/youdi/go/bin
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct // 使用七牛云的
```

注意： go env -w会将配置写到  `GOENV="/Users/youdi/Library/Application Support/go/env"`


下面看下我的配置


```
GO111MODULE="on"
GOARCH="amd64"
GOBIN="/Users/youdi/go/bin"
GOCACHE="/Users/youdi/Library/Caches/go-build"
GOENV="/Users/youdi/Library/Application Support/go/env"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GOINSECURE=""
GONOPROXY=""
GONOSUMDB=""
GOOS="darwin"
GOPATH="/Users/youdi/go"
GOPRIVATE=""
GOPROXY="https://goproxy.cn,direct"
GOROOT="/usr/local/go"
GOSUMDB="off"
GOTMPDIR=""
GOTOOLDIR="/usr/local/go/pkg/tool/darwin_amd64"
GCCGO="gccgo"
AR="ar"
CC="clang"
CXX="clang++"
CGO_ENABLED="1"
GOMOD="/dev/null"
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=/var/folders/8m/v_1j4dgs7rzgqq4p_4_8k_nr0000gn/T/go-build221113671=/tmp/go-build -gno-record-gcc-switches -fno-common"

```


我们看一下，我修改的内容

cat /Users/youdi/Library/Application Support/go/env

```bash
GO111MODULE=on
GOBIN=/Users/youdi/go/bin
GOPROXY=https://goproxy.cn,direct
GOSUMDB=off
```


### GO111MODULE
GO111MODULE 有三个值：off, on和auto（默认值）。

GO111MODULE=off，go命令行将不会支持module功能，寻找依赖包的方式将会沿用旧版本那种通过vendor目录或者GOPATH模式来查找。
GO111MODULE=on，go命令行会使用modules，而一点也不会去GOPATH目录下查找。
GO111MODULE=auto，默认值，go命令行将会根据当前目录来决定是否启用module功能。这种情况下可以分为两种情形：

    当前目录在GOPATH/src之外且该目录包含go.mod文件
    当前文件在包含go.mod文件的目录下面。

当modules功能启用时，依赖包的存放位置变更为$GOPATH/pkg，允许同一个package多个版本并存，且多个项目可以共享缓存的 module

我们看下目录：

cd /Users/youdi/go/pkg
```
├── darwin_amd64
│   ├── github.com
│   ├── go.etcd.io
│   ├── golang
│   ├── golang.org
│   ├── gopkg.in
│   ├── quickstart
│   └── uc.a
├── mod
│   ├── cache
│   ├── github.com
│   ├── golang.org
│   ├── google.golang.org
│   └── gopkg.in
└── sumdb
    └── sum.golang.org
```


### go mod命令

golang 提供了 `go mod`命令来管理包。


go help mod
```bash

Go mod provides access to operations on modules.

Note that support for modules is built into all the go commands,
not just 'go mod'. For example, day-to-day adding, removing, upgrading,
and downgrading of dependencies should be done using 'go get'.
See 'go help modules' for an overview of module functionality.

Usage:

	go mod <command> [arguments]

The commands are:

	download    download modules to local cache
	edit        edit go.mod from tools or scripts
	graph       print module requirement graph
	init        initialize new module in current directory
	tidy        add missing and remove unused modules
	vendor      make vendored copy of dependencies
	verify      verify dependencies have expected content
	why         explain why packages or modules are needed

Use "go help mod <command>" for more information about a command.

```

go mod 有以下命令：

| 命令     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| download | download modules to local cache(下载依赖包)                  |
| edit     | edit go.mod from tools or scripts（编辑go.mod)                |
| graph    | print module requirement graph (打印模块依赖图)              |
| verify     | initialize new module in current directory（在当前目录初始化mod） |
| tidy     | add missing and remove unused modules(拉取缺少的模块，移除不用的模块) |
| vendor   | make vendored copy of dependencies(将依赖复制到vendor下)     |
| verify   | verify dependencies have expected content (验证依赖是否正确） |
| why      | explain why packages or modules are needed(解释为什么需要依赖) |

比较常用的是 `init`,`tidy`, `edit`


### 使用go mod管理一个新项目

#### 1. 初始化项目
可以随便找一个目录创建项目，我使用习惯用IDEA进行创建
```bash
mkdir Gone
cd Gone
go mod init Gone
```

查看一下 go.mod文件

```go
module Gone

go 1.14
```

go.mod文件一旦创建后，它的内容将会被go toolchain全面掌控。go toolchain会在各类命令执行时，比如go get、go build、go mod等修改和维护go.mod文件。


go.mod 提供了module, require、replace和exclude 四个命令

- `module` 语句指定包的名字（路径）
- `require` 语句指定的依赖项模块
- `replace` 语句可以替换依赖项模块
- `exclude` 语句可以忽略依赖项模块

#### 2. 添加依赖

创建 main.go文件

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```


执行 go run main.go 运行代码会发现 go mod 会自动查找依赖自动下载
再查看 `go.mod`

```bash
module Gone

go 1.14

require github.com/gin-gonic/gin v1.6.3
```
go module 安装 package 的原則是先拉最新的 release tag，若无tag则拉最新的commit

go 会自动生成一个 go.sum 文件来记录 dependency tree


![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/JhSGHb.jpg)

再次执行脚本 go run main.go发现跳过了检查并安装依赖的步骤。

可以使用命令 go list -m -u all 来检查可以升级的package，使用go get -u need-upgrade-package 升级后会将新的依赖版本更新到go.mod * 也可以使用 go get -u 升级所有依赖

去mod包缓存下看看

```bash
/Users/youdi/go/pkg/mod/github.com/gin-gonic/gin@v1.6.3
```


### go get升级

* 运行 go get -u 将会升级到最新的次要版本或者修订版本(x.y.z, z是修订版本号， y是次要版本号)
* 运行 go get -u=patch 将会升级到最新的修订版本
* 运行 go get package@version 将会升级到指定的版本号version
* 运行go get如果有版本的更改，那么go.mod文件也会更改


### 使用replace替换无法直接获取的package
由于某些已知的原因，并不是所有的package都能成功下载，比如：golang.org下的包。

modules 可以通过在 go.mod 文件中使用 replace 指令替换成github上对应的库，比如：

```go
replace (
	golang.org/x/crypto v0.0.0-20190313024323-a1f597ede03a => github.com/golang/crypto v0.0.0-20190313024323-a1f597ede03a
)
```

### go mod发布和使用

参考Roberto Selbach写的go mod入门文章，文末，我给出链接


### Creating a Module
如果你设置好go mod了，那你就可以在任何目录下随便创建

```bash
$mkdir gomodone
$cd gomodone
```
在这个目录下创建一个文件`say.go`

```go
package gomodone

import "fmt" 

// say Hi to someone
func SayHi(name string) string {
   return fmt.Sprintf("Hi, %s", name)
}

```

初始化一个 `go.mod`文件

```bash
$ go mod init github.com/jacksonyoudi/gomodone
go: creating new go.mod: module github.com/jacksonyoudi/gomodone
```
查看 go.mod内容如下：

```go
github.com/jacksonyoudi/gomodone
go 1.14
```

下面我们要将这个module发布到github上，然后在另外一个程序使用

```bash
$git init
$vim .gitiiignore
$git commit -am "init"
// github创建对应的repo
$git remote add origin git@github.com:jacksonyoudi/gomodone.git
$git push -u origin master
```

执行完，上面我们就相当于发布完了。

如果有人需要使用，就可以使用
```bash
go get github.com/jacksonyoudi/gomodone
```
这个时候没有加tag，所以，没有版本的控制。默认是v0.0.0后面接上时间和commitid。如下：

```bash
gomodone@v0.0.0-20200517004046-ee882713fd1e
```

官方不建议这样做，没有进行版本控制管理。


### module versioning
使用tag，进行版本控制

#### making a release

```bash
git tag v1.0.0
git push --tags
```
操作完，我们的module就发布了一个v1.0.0的版本了。

推荐在这个状态下，再切出一个分支，用于后续v1.0.0的修复推送,不要直接在master分支修复
```bash
$git checkout -b v1
$git push -u origin v1
```


### use our module
上面已经发布了一个v1.0.0的版本，我们可以在另一个项目中使用，创建一个go的项目

```bash
$mkdir Gone
$cd Gone
$vim main.go
```

```go
package main

import (
	"fmt"
	"github.com/jacksonyoudi/gomodone"
)

func main() {
	fmt.Println(gomodone.SayHi("Roberto"))
}
```

代码写好了，我们生成 go mod文件

```bash
go mod init Gone
```
上面命令执行完，会生成 go mod文件
看下mod文件：

```bash
module Gone

go 1.14

require (
	github.com/jacksonyoudi/gomodone v1.0.0
)
```


```bash
$go mod tidy
go: finding module for package github.com/jacksonyoudi/gomodone
go: found github.com/jacksonyoudi/gomodone in github.com/jacksonyoudi/gomodone v1.0.0
```
同时还生成了go.sum, 其中包含软件包的哈希值，以确保我们具有正确的版本和文件。

```bash
github.com/jacksonyoudi/gomodone v1.0.1 h1:jFd+qZlAB0R3zqrC9kwO8IgPrAdayMUS0rSHMDc/uG8=
github.com/jacksonyoudi/gomodone v1.0.1/go.mod h1:XWi+BLbuiuC2YM8Qz4yQzTSPtHt3T3hrlNN2pNlyA94=
github.com/jacksonyoudi/gomodone/v2 v2.0.0 h1:GpzGeXCx/Xv2ueiZJ8hEhFwLu7xjxLBjkOYSmg8Ya/w=
github.com/jacksonyoudi/gomodone/v2 v2.0.0/go.mod h1:L8uFPSZNHoAhpaePWUfKmGinjufYdw9c2i70xtBorSw=
```
这个内容是下面的，需要操作执行的结果



go run main.go就可以运行了

### Making a bugfix release

假如fix一个bug,我们在v1版本上进行修复

修改代码如下：

```go
// say Hi to someone
func SayHi(name string) string {
-       return fmt.Sprintf("Hi, %s", name)
+       return fmt.Sprintf("Hi, %s!", name)
}
```

修复好，我们开始push

```bash
$ git commit -m "Emphasize our friendliness" say.go
$ git tag v1.0.1
$ git push --tags origin v1
```

#### Updating modules
刚才fix bug，所以要在我们使用项目中更新

这个需要我们手动执行更新module操作

我们通过使用我们的好朋友来做到这一点go get：

- 运行  `go get -u` 以使用最新的  minor  版本或修补程序版本（即它将从1.0.0更新到例如1.0.1，或者，如果可用，则更新为1.1.0）
- 运行  go get -u=patch 以使用最新的  修补程序  版本（即，将更新为1.0.1但不更新  为1.1.0）
- 运行go get package@version 以更新到特定版本（例如github.com/jacksonyoudi/gomodone@v1.0.1）

目前module最新的也是v1.0.1

```bash
// 更新最新
$go get -u
$go get -u=patch
//指定包，指定版本
$go get github.com/jacksonyoudi/gomodone@v1.0.1
```
操作完，go.mod文件会修改如下:

```go
module Gone

go 1.14

require (
	github.com/jacksonyoudi/gomodone v1.0.1
)
```



#### Major versions
根据语义版本语义，主要版本与次要版本  不同。主要版本可能会破坏向后兼容性。从Go模块的角度来看，主要版本是  完全不同的软件包。乍一看这听起来很奇怪，但这是有道理的：两个不兼容的库版本是两个不同的库。
比如下面修改，完全破坏了兼容性。

```go
package gomodone

import (
	"errors"
	"fmt"
)

// Hi returns a friendly greeting
// Hi returns a friendly greeting in language lang
func SayHi(name, lang string) (string, error) {
	switch lang {
	case "en":
		return fmt.Sprintf("Hi, %s!", name), nil
	case "pt":
		return fmt.Sprintf("Oi, %s!", name), nil
	case "es":
		return fmt.Sprintf("¡Hola, %s!", name), nil
	case "fr":
		return fmt.Sprintf("Bonjour, %s!", name), nil
	default:
		return "", errors.New("unknown language")
	}
}
```


如上，我们需要不同的大版本，这种情况下

修改 go.mod如下

```go
module github.com/jacksonyoudi/gomodone/v2

go 1.14
```

然后，重新tag，push

```bash
$ git commit say.go -m "Change Hi to allow multilang"
$ git checkout -b v2 # 用于v2版本，后续修复v2
$ git commit go.mod -m "Bump version to v2"
$ git tag v2.0.0
$ git push --tags origin v2 
```

### Updating to a major version
即使发布了库的新不兼容版本，现有软件 也不会中断，因为它将继续使用现有版本1.0.1。go get -u 将不会获得版本2.0.0。
如果想使用v2.0.0,代码改成如下：

```go
package main

import (
	"fmt"
    "github.com/jacksonyoudi/gomodone/v2"
)

func main() {
	g, err := gomodone.SayHi("Roberto", "pt")
	if err != nil {
		panic(err)
	}
	fmt.Println(g)
}
```
执行 go mod tidy

```bash
go: finding module for package github.com/jacksonyoudi/gomodone/v2
go: downloading github.com/jacksonyoudi/gomodone/v2 v2.0.0
go: found github.com/jacksonyoudi/gomodone/v2 in github.com/jacksonyoudi/gomodone/v2 v2.0.0
```

当然，两个版本都可以同时使用, 使用别名
如下：

```go
package main

import (
	"fmt"
	"github.com/jacksonyoudi/gomodone"
	mv2 "github.com/jacksonyoudi/gomodone/v2"
)

func main() {
	g, err := mv2.SayHi("Roberto", "pt")
	if err != nil {
		panic(err)
	}
	fmt.Println(g)

	fmt.Println(gomodone.SayHi("Roberto"))
}

```

执行一下 `go mod tidy`

### Vendoring

默认是忽略vendor的，如果想在项目目录下有vendor可以执行下面命令
```
$go vendor
```

当然，如果构建程序的时候，希望使用vendor中的依赖，

```go
$ go build -mod vendor
```


### IDEA下开发GO

1. 创建go项目

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/IqR0yI.jpg)

2. 创建完项目，会自动生成go mod文件
   如果需要修改，可以手动修改，加入git等操作

3. 写业务逻辑代码
   
![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/ZNN4tE.jpg)
4. 解决依赖，更新go.mod

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/CxoGM9.jpg)

5. go build
