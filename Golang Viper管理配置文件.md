## Viper

`viper`适用于管理`Go`程序的配置文件。

支持的文件格式有：

* `JSON`
* `TOML`
* `YAML`
* `HCL`
* `envfile`
* `Java properties`

支持的配置来源为：

* 配置文件
* 环境变量
* 远程配置系统
* 命令参数
* buffer
* 显式设置

配置读取优先级为：

* 显示调用`Set`函数
* 命令行参数
* 环境变量
* 配置文件
* key/value存储系统
* 默认值



## 安装

```shell
go get github.com/spf13/viper
```



## 设置默认值

显示设置默认值：

```go
func main(){
    viper.SetDefault("ContentDir", "content")
    fmt.Println(viper.Get("ContentDir"))
}
```



## 读取配置文件

viper支持`JSON`、`TOML`、`YAML`、`HCL`、`envfile`和`Java properties`格式配置文件。

Viper可以搜索多个路径，但是只支持单个配置文件。

`config.yaml`:

```yaml
number:
  one: 1
  two: 2
  three: 3
```

`go`示例：

```go
viper.AddConfigPath(".")
viper.SetConfigName("config")
viper.SetConfigType("yaml")
err := viper.ReadInConfig()
if err != nil {
	log.Fatal(err)
}
fmt.Println(viper.GetInt("number.one"))
```

**注意**：如果一个目录下，有两个同名的配置文件，这时候即使指定了文件类型，也只会加载第一个文件。



## 写入配置文件

* `WriteConfig` - 将当前配置写入预定义路径，并且覆盖（如果存在）。如果没有预定义路径，则报错。
* `SafeWriteConfig` - 当当前配置写入预定义路径。如果没有路径，则报错。如果存在，将不会覆盖。
* `WriteConfigAs` - 将当前配置写入给定的文件路径，并且覆盖（如果存在）。
* `SafeWriteConfigAs` - 将当前配置写入给定的文件路径，不会覆盖（如果存在）。

```go
viper.Set("number.one", 11)
if err := viper.WriteConfig(); err != nil {
	log.Fatal(err)
}
```



## 读取环境变量

* `AutomaticEnv` : viper会在发出`viper.Get`请求时随时检查环境变量。
* `BindEnv` : 第一个参数为键的名称，第二个参数为环境变量的名称。该函数将环境变量名与键名绑定，环境变量名称区分大小写，如果没有提供变量名，那么使用 **前缀 + ”_“ + 键名大写 ** 来匹配环境变量。
* `SetEnvPrefix` : 设置前缀。

```go
viper.AutomaticEnv()
viper.SetEnvPrefix("spf")
viper.BindEnv("id")
os.SetEnv("SPF_ID", 10)
fmt.Println(viper.Get("id"))
```



## 命令参数

`viper`可以使用`pflag`读取命令参数。

`Pflag`安装方法：

```shell
go get github.com/spf13/pflag
```

使用`BindFlags`绑定参数：

```go
pflag.Int("flagname", 1234, "help message for flagname")
pflag.Parse()
viper.BindFlags(pflag.CommandLine)

fmt.Println(viper.GetInt("flagname"))
```

启动时输入:

```go
go run main.go --flagname 2345
```

最终会打印输出`2345`。

`pflag`可以导入`flag`包定义的`flags`：

```go
flag.Int("flagname", 1234, "help message for flagname")
pflag.CommandLine.AddGoFlagSet(flag.CommandLine)
pflag.Parse()
viper.BindPFlags(pflag.CommandLine)

fmt.Println(viper.Get("flagname"))
```



## 从`io.Reader`读取配置

示例：

```go
viper.SetConfigType("yaml")
var yamlExample = []byte(`
Hacker: true
name: steve
hobbies:
- skateboarding
- snowboarding
- go
clothing:
  jacket: leather
  trousers: denim
age: 35
eyes: brown
beard: true
`)
viper.ReadConfig(bytes.NewBuffer(yamlExample))
fmt.Println(viper.Get("name"))
```



## 监听配置文件变化

当配置文件发生变化时，不需要重新启动服务以使配置生效，viper驱动的应用程序可以在运行时读取配置文件的更新。

```go
viper.WatchConfig()
viper.OnConfigChange(func(e fsnotify.Event) {
    fmt.Println("config file changed", e.Name)
})
```



## 覆盖配置

可以重新设置键值对。

```go
viper.Set("Verbose", true)
```



## 使用别名

可以为键设置别名。

```go
viper.RegisterAlias("name1", "name2")

// 两个值相等
fmt.Println(viper.Get("name1"))
fmt.Println(viper.Get("name2"))
```



## 从viper中获取值

* `Get(key string) : interface{}`
* `GetBool(key string) : bool `
* `GetFloat64(key string) : float64`
* `GetInt(key string) : int`
* `GetIntSlice(key string) : []int`
* `GetString(key string) : string`
* `GetStringMap(key string) : map[string]interface{}`
* `GetStringMapString(key string) : map[string]string`
* `GetStringSlice(key string) : []string`
* `GetTime(key string) : time.Duration`
* `IsSet(key string) : bool`
* `AllSettings() : map[string]interface{}`

每一个`Get`方法在找不到值的时候都会返回零值，如果想检测值是否存在，可以使用`IsSet()`方法。



## 提取子树

对于配置文件`config.yaml`:

```yaml
number:
  one: 1
  two: 2
  three: 3
```

获取子树的操作为：

```go
subv := viper.Sub("number")
```



## 反序列化

举例:

```go
type config struct {
    number number
}

type number struct {
	one   int
	two   int
	three int
}

func main(){
	viper.AddConfigPath(".")
	viper.SetConfigName("config")
	viper.SetConfigType("yaml")

	err := viper.ReadInConfig()
	if err != nil {
		log.Fatal(err)
	}
	var c config
	if err = viper.Unmarshal(&c); err != nil {
		log.Fatal(err)
	}

	fmt.Println(viper.Get("number"))
}
```



## 多个viper实例

在程序中可以创建多个不同的viper实例，每个实例可以读取不同的配置文件。

```go
v1 := viper.New()
v2 := viper.New()
```

