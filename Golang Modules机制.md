`Go Modules`机制是`Go`官方的包管理机制，从`Go 1.11`开始作为`experiment feature`引入，`GO111MODULE`是这个特性的开关。

### `GO111MODULE`

`GO111MODULE`有三个值：`auto`、`on`和`off`，默认值为`auto`，`GO111MODULE`的值会影响`Go compiler`的依赖管理模式

> - off：`go compiler`会始终使用`GOPATH mode`，无论要构建的源码目录是否在`GOPATH`路径下，`go compiler`都会在传统`GOPATH`和`vendor`目录下，搜索目标程序依赖的`go package`
> - on：`go compiler`会始终使用`module-aware mode`，无论要构建的源码是否位于`GOPATH`路径下，`go compiler`都会在`go mod`命令的缓存目录`$GOPATH/pkg/mod`下搜索对应版本的依赖`package`
> - auto：使用`GOPATH mode`还是`module-aware mode`，取决于要构建的源码目录是否位于`$GOPATH/src`为根的目录体系下，且是否包含`go.mod`文件

### `go mod`命令

> `golang`使用`go mod`命令来实现包的管理

`go mod`命令说明：

| 命令     | 说明                                              |
| -------- | ------------------------------------------------ |
| download | download modules to local cache                  |
| edit     | edit go.mod from tools or scripts                |
| graph    | print module requirement graph                   |
| init     | initialize new module in current directory       |
| tidy     | add missing and remove unused module             |
| vendor   | make vendored copy of dependencies               |
| verify   | verify dependencies have expected content        |
| why      | explain why packages or modules are needed       |

`go mod`命令使用：

> 1. 执行命令`go mod init`，会在当前目录下生成一个`go.mod`文件。执行这条命令之前，但其那目录如果已经存在`go.mod`文件，需要先删除
> 2. 如果生成的`go mod`文件不完整，则继续执行`go mod tidy`命令，它会添加缺失的模块以及移除不需要的模块，执行后会生成`go.sum`文件
> 3. 执行`go mod verify`来检查当前模块的依赖是否全部都下载完毕，以及下载之后是否被修改，如果依赖没有问题，则会打印`all modules verified`
> 4. 执行命令`go mod vendor`生成`vendor`目录，该目录下存放的是`go.mod`文件描述的依赖包，以及一个`modules.txt`

### `go mod`相关文件

执行命令`go mod init`以及`go mod tidy`之后，会生成两个文件，分别为：

> - `go.mod`：包含模块名称、`go`的版本、该模块的依赖信息
> - `go.sum`：模块所有依赖的校验和