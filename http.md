对于编程语言来说，支持网络通信是极为重要的，而go内置的`net/http`标准库，则很好的实现了`http`通信协议相关特性。通过构造`http`客户端或者服务器，可以很好的进行网络开发。

### 客户端基本用法

我们可以通过以下方法，发起`http`请求

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





