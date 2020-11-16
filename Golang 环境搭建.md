## `Linux`下安装`GO`

### 一、查找需要的版本

> `GOlang`官网下载地址：[https://golang.org/dl/](https://golang.org/dl/)
> 
> `Golang`国内下载网址：[https://studygolang.com/dl](https://studygolang.com/dl)

在官网选择一个版本，复制下载链接地址

### 二、 创建一个存放安装包的目录，将安装包下载到对应目录

```shell
# 创建go目录，并且进入go目录
mkdir ~/go && cd ~/go
# 下载安装包
wget https://dl.google.com/go/go1.14.3.linux-amd64.tar.gz
```

### 三、解压

解压安装包到`/usr/local`目录下（官方推荐）：
```shell
tar -C /usr/local -zxvf go1.14.3.linux-amd64.tar.gz
```

### 四、添加环境变量

在`/etc/profile`或者`$HOME/.profile`中添加以下文本：
```shell
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
```

保存退出，然后`source`一下：
```shell
source /etc/profile
```

### 五、执行`go version`，如果显示版本号，则安装成功
```shell
go version
```

### 六、创建工作空间`workspaces`，官方推荐是`$HOME/go`

创建`GOPATH`目录
```shell
mkdir ~/go
```

一般`GOPATH`下会有三个文件夹：`bin` 、`pkg`、`src`
> `bin`: 编译后的可执行文件存放路径
>
> `pkg`: 编译包时，生成`.a`文件的存放路径
>
> `src`: 源码路径

### 七、将工作空间路径设置到环境变量中

打开`~/.bashrc`文件，在最后添加如下文本：
```shell
export GOPATH=$HOME/go
export PATH=$PATH:/GOPATH/bin
```

保存退出后，`source`一下
```shell
source ~/.bashrc
```

### 八、编写`go`程序

接下来就可以开始编写`go`程序啦。