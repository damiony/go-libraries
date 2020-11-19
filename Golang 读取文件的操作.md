`Golang`提供了以下几种读取文件的方法。

### `file.Read`

```go
  1 package main
  2
  3 import (
  4     "fmt"
  5     "io"
  6     "log"
  7     "os"
  8 )
  9
 10 func main() {
 11     file, err := os.Open("./data.txt")
 12     if err != nil {
 13         log.Fatal(err)
 14     }
 15     defer file.Close()
 16
 17     b := make([]byte, 128)
 18     for {
 19         n, err := file.Read(b)
 20         if err == io.EOF {
 21             break
 22         }
 23         if err != nil {
 24             log.Fatal(err)
 25         }
 26
 27         fmt.Printf("%s", string(b[:n]))
 28     }
 29 }
```

### `io.ReadFull`

```go
  1 package main
  2
  3 import (
  4     "fmt"
  5     "io"
  6     "log"
  7     "os"
  8 )
  9
 10 func main() {
 11     file, err := os.Open("./data.txt")
 12     if err != nil {
 13         log.Fatal(err)
 14     }
 15		defer file.Close()
 16
 17     // 读取正好1024个字节，内容不够会报错
 18     b := make([]byte, 1024)
 19     n, err := io.ReadFull(file, b)
 20     if err != nil {
 21         log.Fatal(err)
 22     }
 23
 24     fmt.Printf("%s", string(b[:n]))
 25 }
```

### `io.ReadAtLeast`

```go
  1 package main
  2
  3 import (
  4     "fmt"
  5     "io"
  6     "log"
  7     "os"
  8 )
  9
 10 func main() {
 11     file, err := os.Open("./data.txt")
 12     if err != nil {
 13         log.Fatal(err)
 14     }
 15     defer file.Close()
 16
 17     // 至少读取8个字节，不够的话会报错
 18     b := make([]byte, 1024)
 19     n, err := io.ReadAtLeast(file, b, 8)
 20     if err != nil {
 21         log.Fatal(err)
 22     }
 23
 24     fmt.Printf("%s", string(b[:n]))
 25 }
```

### `bufio.NewReader`

```go
  1 package main
  2
  3 import (
  4     "bufio"
  5     "fmt"
  6     "io"
  7     "log"
  8     "os"
  9 )
 10
 11 func main() {
 12     file, err := os.Open("./data.txt")
 13     if err != nil {
 14         log.Fatal(err)
 15     }
 16     defer file.Close()
 17
 18     reader := bufio.NewReader(file)
 19     for {
 20         str, err := reader.ReadString('\n')
 21         if err == io.EOF {
 22             break
 23         }
 24         if err != nil {
 25             log.Fatal(err)
 26         }
 27         fmt.Printf("%s", str)
 28     }
 29 }
```

### `ioutil.ReadAll`

```go
  1 package main
  2
  3 import (
  4     "fmt"
  5     "io/ioutil"
  6     "log"
  7     "os"
  8 )
  9
 10 func main() {
 11     file, err := os.Open("./data.txt")
 12     if err != nil {
 13         log.Fatal(err)
 14     }
 15     defer file.Close()
 16
 17     data, err := ioutil.ReadAll(file)
 18     if err != nil {
 19         log.Fatal(err)
 20     }
 21
 22     fmt.Printf("%s", string(data))
 23 }
```

### `ioutil.ReadFile`

```go
  1 package main
  2
  3 import (
  4     "fmt"
  5     "io/ioutil"
  6     "log"
  7 )
  8
  9 func main() {
 10     b, err := ioutil.ReadFile("./data.txt")
 11     if err != nil {
 12         log.Fatal(err)
 13     }
 14     fmt.Println(string(b))
 15 }
```

### `bufio.NewScanner`

```go
  1 package main
  2
  3 import (
  4     "bufio"
  5     "fmt"
  6     "log"
  7     "os"
  8 )
  9
 10 func main() {
 11     file, err := os.Open("./data.txt")
 12     if err != nil {
 13         log.Fatal(err)
 14     }
 15     defer file.Close()
 16
 17     scanner := bufio.NewScanner(file)
 18     for scanner.Scan() {
 19         fmt.Println(scanner.Text())
 20     }
 21     err = scanner.Err()
 22     if err != nil {
 23         log.Fatal(err)
 24     }
 25 }
```

