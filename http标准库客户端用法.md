对于编程语言来说，网络通信是很重要的功能，而go内置的`net/http`标准库，则很好的实现了`http`通信协议相关特性。通过构造`http`客户端或者服务器，可以很好的进行网络开发。

## 客户端基本用法

### HTTP 请求

我们可以通过以下方法，发起`http`请求：

```go
// GET请求
func (c *Client) Get(url string) (r *Response, err error)
// POST请求
func (c *Client) Post(url string, bodyType string, body io.Reader) (r *Response, err error)
// PostForm请求
func (c *Client) PostForm(url string, data url.Values) (r *Response, err error)
// HEAD请求
func (c *Client) Head(url string) (r *Response, err error)
// DO方法
func (c *Client) Do(req *Request) (resp *Response, err error)
```

> `Get、POST、PostForm、Head`是相应`http`请求方法的快捷实现。
>
> 底层使用的都是`NewRequest`和`DefaultClient.Do`，`DefaultClient`的值被初始化为`http.Client{}`。
>
> 如果想实现定制化的`http`客户端，则可以使用`http.NewRequest`和`http.Client`进行实现。

#### GET 请求

示例：

```go
package main

import (
	"fmt"
	"io/ioutil"
	"log"
	"net/http"
)

func main() {
	resp, err := http.Get("http://www.baidu.com")
	if err != nil {
		log.Fatal(err)
	}
	defer resp.Body.Close()

	result, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("%s", result)
}
```

可以将`http.Get(url)`替换成：

```go
req, err := http.NewRequest("GET", url, nil)
...
clt := http.Client{}
resp, err := clt.Do(req)
```

#### POST 请求

示例：

```go
package main

import (
	"fmt"
	"io/ioutil"
	"log"
	"net/http"
	"strings"
)

func main() {
	resp, err := http.Post("http://localhost:30000", "application/x-www-form-urlencoded", strings.NewReader("name=hahahaha"))
	if err != nil {
		log.Fatal(err)
	}
	defer resp.Body.Close()
	result, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("%s", result)
}
```

- 如果`http.Post`的第二个参数不为`application/x-www-form-urlencoded`，则请求体内的参数将无效。

- `Post`方法的第三个参数为`io.Reader`接口，任何实现了`Read`方法的类型实例，都实现了该接口。

- `strings.NewReader`可以生成一个`Reader`，并且返回指向该`Reader`的指针，该`Reader`实现了`Read`方法。

- `http.Post()`可以修改为：

  ```go
  req, err := NewRequest("POST", url, body)
  ...
  req.Header.Set("Content-Type", contentType)
  clt := http.Client{}
  clt.Do(req)
  ```

  这里用到了`req.Header`，该结构体将在下文说明。

#### PostForm 请求

示例：

```go
package main

import (
	"fmt"
	"io/ioutil"
	"log"
	"net/http"
	"net/url"
)

func main() {
	resp, err := http.PostForm("http://localhost:30000", url.Values{"name": {"hahaha"}})
	if err != nil {
		log.Fatal(err)
	}
	defer resp.Body.Close()
	result, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("%s", result)
}
```

- `PostForm`的请求体参数类型，被默认设置为`application/x-www-form-urlencoded`

- `PostForm`其实是`Post`方法的封装。

  底层实现为:

  ```go
  DefaultClient.Post(url, "application/x-www-form-urlencoded", strings.NewReader(data.Encode()))
  ```

  此处的`data`类型为`url.Values`

- `url.Values`是一个`map`类型。

  结构定义为：

  ```go
  type Values map[string][]string
  ```

  `Values`定义了5个方法：

  ```go
  // 如果key不存在，返回空字符串，如果key存在，返回values切片的第一个值
  func (v Values) Get(key string) string
  // 如果key存在，则替换原有的slice，如果key不存在，则添加新的slice
  func (v Values) Set(key, value string)
  // 以追加方式存入与key对应的slice
  func (v Values) Add(key, value string)
  // 删除key
  func (v Values) Del(key string)
  // 将map按照键值对的顺序，编码成"URL编码"形式，如“bar=baz&foo=quux”，并且键会被排序
  func (v Values) Encode() string
  ```

#### HEAD 请求

示例：

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func main() {
	resp, err := http.Head("http://localhost:30000")
	if err != nil {
		log.Fatal(err)
	}
	defer resp.Body.Close()
	header := resp.Header
	fmt.Println(header)
}
```

- `http.Head`可以替换成：

  ```go
  req, err := NewRequest("HEAD", url, nil)
  ...
  clt := http.Client
  clt.Do(req)
  ```

### Request

`NewRequest`会生成并返回一个`*Request`，`Request`代表了客户端发送给服务端的请求，或者服务端收到的来自客户端的请求。

具体定义为:

```go
type Request struct {
  Method string
  URL *url.URL
  Proto string
  ProtoMajor int
  ProtoMinor int
  Header Header
  Body io.ReadCloser
  GetBody func() (io.ReadCloser, error)
  ContentLength int64
  TransferEncoding []string
  Close bool
  Host string
  Form url.Values
  PostForm url.Values
  MartipartForm *multipart.Form
  Trailer Header
  RemoteAddr string
  RequestURI string
  TLS *tls.ConnectionState
  Cancel <-chan struct{}
  Response *Response
}
```

#### Header

- `http`请求头的值都放在`Header`中，定义为：

  ```go
  type Header map[string][]string
  ```

- `Header`提供了若干个可导出的方法：

  ```go
  // 可导出的方法
  // 以追加的方式设置key
  func (h Header) Add(key, value string)
  // 重新设置新值
  func (h Header) Set(key, value string)
  // 如果key存在，返回slice的第一个值（索引为0），如果不存在，返回空字符串
  func (h Header) Get(key string) string
  // 获取与key相对的value值，如果h是nil，返回nil，否则返回[]string
  func (h Header) Values(key string) []string
  // 删除key
  func (h Header) Del(key string)
  ```

  在以上方法的内部，`h`被类型转换为`textproto.MIMEHeader`，`key`被类型转换为`CanonicalMIMEHeaderKey`。

- `Header`还提供了若干个非导出的方法：

  ```go
  // 不可导出的方法
  // 如果key存在，返回slice的第一个值（索引为0），如果不存在，返回空字符串
  func (h Header) get(key string) string
  // 判断是否有key
  func (h Header) has(key string) bool
  ```

#### Form

- `Form`的类型为：`url.Values`

- `Form`包含了被解析的表单数据，包括`URL`请求字段，以及`PATCH`、`POST`和`PUT`的表单数据。
- 调用`ParseForm`后，`Form`才会包含客户端传送给服务端的表单数据

#### PostForm

- 与`Form`不同的是，该字段不包含`URL`请求数据，其他都相同

#### MultipartForm

- `MultipartForm`包含被上传的文件，执行`ParseMultipartForm`后该文件可读

#### ParseForm

- `ParseForm`解析`URL`请求参数，并且放入`Form`

- `ParseForm`解析`POST`、`PUT`、`PATCH`请求体，并且将结果放入`Form`和`PostForm`。在`Form`中，请求体的参数优先级要高于`URL`查询字符串参数

- 如果请求体类型不为`application/x-www-form-urlencoded`，则请求体参数不能被读取出来
