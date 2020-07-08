对于编程语言来说，网络通信是很重要的功能，而go内置的`net/http`标准库，则很好的实现了`http`通信协议相关特性。通过构造`http`客户端或者服务器，可以很好的进行网络开发。

## 客户端基本用法

### HTTP请求

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

#### GET请求

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

#### POST请求

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

如果`http.Post`的第二个参数不为`application/x-www-form-urlencoded`，则请求体内的参数将无效。

#### PostForm请求

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

`PostForm`的请求体参数类型，被默认设置为`application/x-www-form-urlencoded`

#### HEAD请求

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

### Request

`Request`代表了客户端发送给服务端的请求，或者服务端收到的来自客户端的请求。

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
