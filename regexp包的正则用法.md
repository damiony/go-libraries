做爬虫的时候，往往需要对爬取的内容进行文本搜索，此时使用正则表达式进行模式匹配是一种优雅的做法。`golang`的标准库`regexp`提供了正则表达式的相关用法。

## regexp语法规则

如果想要利用正则表达式直接进行模式匹配，比较常见的是利用`regexp.Match`方法、`regexp.MatchString`方法和`Regexp`类型。

### `regexp.Match`

定义：

```go
func Match(pattern string, b []byte) (matched bool, err error)
```

该方法可判断`byte slice`是否匹配正则表达式，并且返回匹配结果，以及`error`。

示例：

```go
matched, _ := regexp.Match("b", []byte("hello golang"))
fmt.Println(matched) // false
```

### `regexp.MatchString`

定义：

```go
func Match(pattern string, s string) (matched bool, err error)
```

该方法可判断`string`是否匹配正则表达式，并且返回匹配结果，以及`error`。

示例：

```go
matched, _ := regexp.MatchString("b", "hello golang")
fmt.Println(matched)
```

### `Regexp`

类型定义：

```go
type Regexp struct {}
```

`Regexp`是编译后的正则表达式，可以被多个`goroutines`并发使用。

`Regexp`类型示例的获取方法，常见的为`Compile`和`MustCompile`。

#### `Compile`

该方法编译正则表达式，然后将编译后的结果返回。方法定义为：

```go
func Compile(expr string) (*Regexp, error)
```

#### `MustCompile`

该方法和`Compile`很类似，但是如果编译发生错误，不会返回错误，而是直接`panic`。

## Regexp常见的匹配方法

`Regexp`包含很多匹配方法，下面将列举说明较为常见的。

### `Find`

该方法返回第一个匹配的`[]byte`，定义为：

```go
func (re *Regexp) Find(b []byte) []byte
```

示例：

```go
re := regexp.MustCompile("a")
match := re.Find([]byte("hello golang"))
fmt.Println(string(match)) // a
```

### `FindAll`

该方法返回由匹配结果组成的`slice`，定义为：

```go
func (re *Regexp) FindAll(b []byte, n int) [][]byte
```

返回的`slice`长度由参数`n`指定：

当`n < 0`时，返回所有匹配个数；

当`n >= 0`且`n <= 总匹配个数`，返回n个结果；

当`n > 总匹配个数`，返回所有结果。

示例：

```go
re := regexp.MustCompile("l[a-z]")
match := re.FindAll([]byte("hello world, hello golang"), -1)
for _, m := range match {
	fmt.Println(string(m))
}
// ll
// ld
// ll
// la
```

### `FindString`

该方法返回匹配的字符串，定义为：

```go
func (re *Regexp) FindString (s string) string
```

如：

```go
re := regexp.MustCompile("l[a-z]")
match := re.FindString("hello world, hello golang")
fmt.Println(match) // ll
```

### `FindAllString`

定义为：

```go
func (re *Regexp) FindAllString(s string, n int) []string
```

该方法根据参数`n`，返回匹配结果，如果匹配不成功，则返回nil

示例：

```go
re := regexp.MustCompile("l[a-z]")
match := re.FindAllString("hello world, hello golang", -1)
for _, m := range match {
	fmt.Println(string(m))
}
// ll
// ld
// ll
// la
```

### `FindIndex`

该方法第一个匹配结果在原始字符串中的位置，定义为：

```go
func (re *Regexp) FindIndex(b []byte) (loc []int)
```

`loc[0]`是匹配结果的开始位置，`loc[1]`是匹配结果的结束位置`+1`，如果找不到匹配，则返回空`slice`。

示例：

```go
re := regexp.MustCompile("l[a-z]")
match := re.FindIndex([]byte("hello world, hello golang"))
fmt.Println(match) // [2 4]
```

### `FinAllIndex`

该方法是`FindIndex`的`all`版本，它根据`n`参数，决定返回结果的个数。具体定义为：

```go
func (re *Regexp) FindAllIndex(b []byte, n int) [][]int
```

`n`的用法参见`FindAll`。

示例：

```go
re := regexp.MustCompile("l[a-z]")
match := re.FindAllIndex([]byte("hello world, hello golang"), -1)
for _, m := range match {
	fmt.Println(m)
}
// [2 4] [9 11] [15 17] [21 23]
```

### `FindStringIndex`

该方法的作用同`FindIndex`，只是传入的参数类型不同，定义为：

```go
func (re *Regexp) FindStringIndex(s string) (loc []int)
```

### `FindAllStringIndex`

该方法是`FindStringIndex`的`all`版本。具体定义为：

```go
func (re *Regexp) FindAllStringIndex(s string, n int) [][]int
```

### `FindStringSubmatch`

该方法返回匹配的字符串组，定义为：

```go
func (re *Regexp) FindStringSubmatch(s string) []string
```

仅仅看说明可能难以理解该方法的作用，下面举例说明：

```go
...
re := regexp.MustCompile(`(aaa)bb(c)`)
fmt.Printf("%q\n", re.FindStringSubmatch("aaabbc"))
```

返回结果为：

```go
["aaabbc", "aaa", "c"]
```

### `FindAllStringSubmatch`

该方法是`FindStringSubmatch`的`all`版本，具体实现为：

```go
func (re *Regexp) FindAllStringSubmatch(s string, n int) [][]string
```

### `Match`

该方法判断`byte slice`是否匹配正则表达式，定义：

```go
func (re *Regexp) Match(b []byte) bool
```

示例：

```go
re := regexp.MustCompile(`hello`)
match := re.Match([]byte("hello everyone"))
fmt.Println(match) // true
```

### `MatchString`

判断字符串是否匹配正则表达式，定义

```go
func (re *Regexp) MatchString(s string) bool
```

示例：

```go
re := regexp.MustCompile(`hello`)
match := re.MatchString("hello everyone")
fmt.Println(match) // true
```

### `ReplaceAll`

定义为：

```go
func (re *Regexp) ReplaceAll(src, repl []byte) []byte
```

该方法返回`src`的一个拷贝，该拷贝中的所有匹配项都会被`repl`替换。

示例：

```go
re := regexp.MustCompile(`hello`)
match := re.ReplaceAll([]byte("hello everyone"), []byte("hi!"))
fmt.Println(string(match)) // hi! everyone
```

### `ReplaceAllString`

定义为：

```go
func (re *Regexp) ReplaceAllString(src, repl string) string
```

该方法返回`src`的一个拷贝，该拷贝的所有匹配项都会被`repl`替换。

示例：

```go
re := regexp.MustCompile(`hello`)
match := re.ReplaceAllString("hello everyone", "hi!")
fmt.Println(match) // hi! everyone
```

### `Split`

定义为：

```go
func (re *Regexp) Split(s string, n int) []string
```

该方法以匹配项作为分割符，将`s`分割成多个字符串，并且返回字符串组成的切片。

示例：

```go
	re := regexp.MustCompile(`a`)
	s := re.Split("abacadaeafff", -1)
	fmt.Println(s) // ["", "b", "c", "d", "e", "fff"]
```

参数`n`控制返回的`slice`长度：

> n > 0：返回最多n个字符串，最后一个是剩余未进行切割的部分
>
> n == 0：返回nil
>
> n < 0：返回所有字符串
