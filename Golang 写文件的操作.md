`Golang`提供了几种写文件的操作。

### `file.Write`

```go
  1 package main
  2
  3 import (
  4     "fmt"
  5     "log"
  6     "os"
  7 )
  8
  9 func main() {
 10     file, err := os.OpenFile("./data.txt", os.O_WRONLY|os.O_CREATE, 0644)
 11     if err != nil {
 12         log.Fatal(err)
 13     }
 14     defer file.Close()
 15
 16     b := []byte("Hello")
 17     n, err := file.Write(b)
 18     if err != nil {
 19         log.Fatal(err)
 20     }
 21     fmt.Printf("Wrote %d bytes.\n", n)
 22 }
```

### `ioutil.WriteFile`

```go
  1 package main
  2
  3 import (
  4     "io/ioutil"
  5     "log"
  6 )
  7
  8 func main() {
  9     err := ioutil.WriteFile("./data.txt", []byte("Hello"), 0644)
 10     if err != nil {
 11         log.Fatal(err)
 12     }
 13 }
```

### `bufio.NewWriter`

```go
  1 package main
  2
  3 import (
  4     "bufio"
  5     "log"
  6     "os"
  7 )
  8
  9 func main() {
 10     file, err := os.OpenFile("./data.txt", os.O_CREATE|os.O_WRONLY|os.O_TRUNC, 0644)
 11     if err != nil {
 12         log.Fatal(err)
 13     }
 14     defer file.Close()
 15
 16     writer := bufio.NewWriter(file)
 17     writer.WriteString("Hello world")
 18     writer.Flush()
 19 }
```

