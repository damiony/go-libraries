`Container/list`是`Golang`实现的双向链表。

## 定义

(1) `Element`

`Element`的定义为：

```go
type Element struct {
	// 后一个、前一个链表元素指针
	next, prev *Element
	
	// 元素所属链表的指针
	list *List
	
	// 元素的值
	Value interface{}
}
```

(2) `List`

`List`的定义为：

```go
type List struct {
	root Element // sentinel list element, only &root, root.prev, and root.next are used
	len  int     // current list length excluding (this) sentinel element
}
```

为了方便运算，内部使用`root`元素将双向链表的首尾进行连接，但是该元素不算在双向链表内，即包外部无法获取。

## 初始化

有三种初始化方法。

(1) `list.New()`

```go
l := list.New()
```

(2) `new(list.List)`

```go
l := new(list.List).Init()
```

(3) `var`

```go
var l list.List
l.Init()
```

## 查询

(1) 获取第一个元素，没有则返回`nil`。

```go
func (l *List) Front() *Element
```

(2) 获取最后一个元素，没有则返回`nil`。

```go
func (l *List) Back() *Element
```

## 添加

(1) 在链表尾部添加任意值。

```go
func (l *List) PushBack(v interface{}) *Element
```

(2) 在链表头部添加任意值。

```go
func (l *List) PushFront(v interface{}) *Element
```

(3) 在指定元素后添加任意值。

```go
func (l *List) InsertAfter(v interface{}, mark *Element) *Element
```

(4) 在指定元素前添加任意值。

```go
func (l *List) InsertBefore(v interface{}, mark *Element) *Element
```

(5) 将其他链表拼接到尾部。

```go
func (l *List) PushBackList(other *List)
```

(6) 将其他链表拼接到头部。

```go
func (l *List) PushFrontList(other *List)
```

## 移动

(1) 将元素移动到指定位置后面。

```go
func (l *List) MoveAfter(e, mark *Element)
```

(2) 将元素移动到指定元素前面。

```go
func (l *List) MoveBefore(e, mark *Element)
```

(3) 将元素移动到链表尾部。

```go
func (l *List) MoveToBack(e *Element)
```

(4) 将元素移动到链表头部。

```go
func (l *List) MoveToFront(e *Element)
```

## 删除

(1) 删除链表元素，可以看一下删除时避免内存泄露的做法。

```go
func (l *List) Remove(e *Element) interface{}
```

## 其他

(1) 获取链表长度。

```go
func (l *List) Len() int
```