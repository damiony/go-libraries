## `UDP`协议

`UDP`是一种无连接的传输层协议，不需要建立连接就能直接进行数据发送和接收。`UDP`不可靠且无时序，但是实时性比较高。

## `UDP`服务器

实现`UDP`服务器涉及到的方法为：

* `ListenUDP`
* `ReadFromUDP`
* `WriteToUDP`

```go
  1 package main
  2
  3 import (
  4     "fmt"
  5     "net"
  6 )
  7
  8 func main() {
  9     listen, err := net.ListenUDP("udp", &net.UDPAddr{
 10         IP:   net.IPv4(0, 0, 0, 0),
 11         Port: 30000,
 12     })
 13     if err != nil {
 14         fmt.Println("listen failed, err:", err)
 15         return
 16     }
 17     defer listen.Close()
 18
 19     // 循环读数据
 20     for {
 21         var data [1024]byte
 22         n, addr, err := listen.ReadFromUDP(data[:])
 23         if err != nil {
 24             fmt.Println("read udp failed, err:", err)
 25             break
 26         }
 27
 28         go func() {
 29             fmt.Printf("data:%v addr:%v count:%v\n", string(data[:n]), addr, n)
 30             _, err = listen.WriteToUDP(data[:n], addr)
 31             if err != nil {
 32                 fmt.Println("write to udp failed, err:", err)
 33                 continue
 34             }
 35         }()
 36     }
 37 }
```

## `UDP`客户端

实现`UDP`客户端涉及到的方法为：

* `Dial`
* `Write`
* `ReadFromUDP`

```go
  1 package main
  2
  3 import (
  4     "fmt"
  5     "log"
  6     "net"
  7     "time"
  8 )
  9
 10 func main() {
 11     socket, err := net.DialUDP("udp", nil, &net.UDPAddr{
 12         IP:   net.IPv4(0, 0, 0, 0),
 13         Port: 30000,
 14     })
 15     if err != nil {
 16         log.Fatal(err)
 17     }
 18     defer socket.Close()
 19
 20     for {
 21         _, err := socket.Write([]byte("Hello World"))
 22         if err != nil {
 23             log.Fatal(err)
 24         }
 25
 26         result := make([]byte, 1024)
 27         n, remoteAddr, err := socket.ReadFromUDP(result)
 28         if err != nil {
 29             log.Fatal("Read from udp server err:", err)
 30         }
 31         fmt.Printf("Received msg from %s, data %s\n", remoteAddr, string(result[:n]))
 32
 33         time.Sleep(time.Second * 5)
 34     }
 35 }
```

