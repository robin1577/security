# go

> https://github.com/coderit666/GoGuide

## 基础

go的一些命令

- `go env  #查看当前go的环境变量,所有和go有关的配置都在这里`

    - ```bash
        D:\pentest_tool\CodeQL\go_project\Vulnerability-goapp>go env
        #go的包管理模式
        set GO111MODULE=auto
        
        set GOARCH=amd64
        set GOBIN=
        set GOCACHE=C:\Users\lambo\AppData\Local\go-build
        set GOENV=C:\Users\lambo\AppData\Roaming\go\env
        set GOEXE=.exe
        set GOEXPERIMENT=
        set GOFLAGS=
        set GOHOSTARCH=amd64
        set GOHOSTOS=windows
        set GOINSECURE=
        set GOMODCACHE=C:\Users\lambo\go\pkg\mod
        set GONOPROXY=
        set GONOSUMDB=
        set GOOS=windows
        #全局软件包依赖所在的地方，基本可以不用，都用go.mod（类型maven的pom.xml）文件来设置软件包依赖
        set GOPATH=C:\Users\lambo\go
        set GOPRIVATE=
        
        #go的软件源，换成国内源
        set GOPROXY=https://goproxy.cn,direct
        set GOROOT=D:\app\go
        set GOSUMDB=sum.golang.org
        set GOTMPDIR=
        set GOTOOLDIR=D:\app\go\pkg\tool\windows_amd64
        set GOVCS=
        #go的版本
        set GOVERSION=go1.18.3
        set GCCGO=gccgo
        set GOAMD64=v1
        set AR=ar
        set CC=gcc
        set CXX=g++
        set CGO_ENABLED=1
        #不同目录下，GOMOD值不一样，表示当前目录的配置文件的路径
        set GOMOD=D:\pentest_tool\CodeQL\go_project\Vulnerability-goapp\go.mod
        set GOWORK=
        set CGO_CFLAGS=-g -O2
        set CGO_CPPFLAGS=
        set CGO_CXXFLAGS=-g -O2
        set CGO_FFLAGS=-g -O2
        set CGO_LDFLAGS=-g -O2
        set PKG_CONFIG=pkg-config
        set GOGCCFLAGS=-m64 -mthreads -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=C:\Users\lambo\AppData\Local\Temp\go-build3622063079=/tmp/go-build -gno-record-gcc-switches
        ```

    - **GO111MODULE ，这到底什么？**

        - > https://zhuanlan.zhihu.com/p/452837197

        - 当 `Go` 在 2009 年首次推出时，它并没有随包管理器一起提供。取而代之的是`go get`，通过使用它们的导入路径来获取所有源并将其存储在 `$ GOPATH / src` 中。

        - 无法实现类似`npm install`或者`pip install -r requirements`这样的自动导入所有需要的包。并且go项目也被限制在了go的PATH文件夹中,导入也需要写较长的路径,十分不方便。GO111MODULE的出现就是用来解决上述所有的问题。

        - `Go 1.11` 引入了 `Go` 模块。 `Go Modules` 不使用 `GOPATH`存储每个软件包的单个 `git checkout`，而是存储带有 `go.mod` 标记版本的标记版本，并跟踪每个软件包的版本。

            - **也就是要么使用全局的依赖包** ，**GO111MODULE=off**
            - **要么自己在go.mod设置依赖包，GO111MODULE=on**

        - 从那时起，`『GOPATH 行为』`与`『Go Modules 行为』`之间的交互已成为 `Go` 的最大难题之一。一个环境变量造成了 95％的痛苦：`GO111MODULE`。

        - `GO111MODULE` 是一个环境变量，可以在使用 `go` 更改 `Go` 导入包的方式时进行设置。第一个痛点是，根据 `Go` 版本，其语义会发生变化。

        - 在 `Go 1.13以后版本`， `GO111MODULE` 的默认行为 (`**auto**`) 改变了：

            - 当存在 `go.mod` 文件时或处于 `GOPATH` 外， 其行为均会等同于于 `GO111MODULE=on`。表示需要go.mod 来存储项目依赖包版本，类似maven的pom.xml吧。
            - 当处于 `GOPATH` 内且没有 `go.mod` 文件存在时其行为会等同于 `GO111MODULE=off`。

- **go换国内源**

     ```bash
     #Go 设置国内镜像源:https://goproxy.cn
     go env -w GO111MODULE=on
     go env -w GOPROXY=https://goproxy.cn,direct
     ```

- ```bash
    go version 查看当前安装的go版本
    go fmt 格式化当前目录下的go代码
    	会将指定文件中凌乱的代码按照go语言规范格式化
    go run 命令文件 #编译并运行go程序
    	#package main包中包含main函数的文件, 我们称之为命令文件。 
    	# go run main.go 就会启动一个go项目，比如一个web项目，很方便啊
    	#其它包中的文件, 我们称之为源码文件
    ```

### **go mod**

包管理工具，类似maven，go的版本为1.11以上。

go mod 有以下命令：

| 命令     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| download | download modules to local cache(下载依赖包)                  |
| edit     | edit go.mod from tools or scripts（编辑go.mod)               |
| graph    | print module requirement graph (打印模块依赖图)              |
| verify   | initialize new module in current directory（在当前目录初始化mod） |
| tidy     | add missing and remove unused modules(拉取缺少的模块，移除不用的模块) |
| vendor   | make vendored copy of dependencies(将依赖复制到vendor下)     |
| verify   | verify dependencies have expected content (验证依赖是否正确） |
| why      | explain why packages or modules are needed(解释为什么需要依赖) |
| init     | initialize new module in current directory (在当前文件夹下初始化一个新的module, 创建go.mod文件)) |



比较常用的是 `init`,`tidy`, `edit`

## codql——go

Go 自动构建器尝试在存储库中自动检测在 Go 中编写的代码，并且仅运行构建脚本以尝试获取依赖项。

若要强制 CodeQL 将提取限制为由生成脚本编译的文件，请将环境变量设置为 CODEQL_EXTRACTOR_GO_BUILD_TRACING=on 或使`--command`选项指定生成命令。

codeql扫描go的时候会把依赖库也扫描，待解决。看了一些文章，go好像也就是直接创建。

`codeql database create ./codeql_result/target_database  --language=go --source-root=./ --overwrite --threads=8"`



