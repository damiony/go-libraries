# `YAML`

**`yaml`** 是一种**数据序列化语言**，可以配合大多数编程语言使用。

主要应用于**编写配置文件**，一般以**`.yaml`**作为文件后缀。

## 基本语法

### 大小写敏感

```yaml
One: 1
one: 1
```

### 用缩进表示层级关系

缩进只能用空格，不能用`tab`。

缩进空格数量不重要，但是同一层级元素必须左侧对齐。

```yaml
one:
  two: 2
  three:
    four: 4
    five: 5
```

### 用`#`表示注释

只支持单行注释。

```yaml
# 注释1
# 注释2
```

### 一个文件可以包含多个文件的内容

用`---`表示一份内容的开始。

用`...`表示一份内容的结束。

```shell
---
one: 1
...

---
two: 2
...
```

### 对象

以键值对形式出现，用 **冒号 + 空格** 分开 **键** 与 **值**。

```yaml
key1: value1
```

支持多层嵌套。

```yaml
key1:
  child-key1: value1
  child-key2: value2
```

支持流式风格（用花括号包裹，用逗号加空格分隔）。

```yaml
key1: { child-key1: value1, child-key2: value2 }
```

### 数组

数组数据以 **破折号 + 空格** 开头。

```yaml
# values: ["value1", "value2", "value3"]
values:
  - value1
  - value2
  - value3
```

支持内联格式（用方括号包裹，逗号加空格分隔）。

```yaml
values: [value1, value2, value3]
```

支持多维数组。

```yaml
# "values": [["value1", "value2"], ["value3", "value4"]]
values:
  -
    - value1
    - value2
  -
    - value3
    - value4
```

### 字符串

一般不需要用引号包裹，但是如果字符串中使用了`"\"`开头的转义字符，就必须使用引号包裹。

```yaml
strings:
  - Hello world
  - 'Hello World'
  - "Hello world"
  - "Hello world\n"
  - 'He said: "Hello"!'
```

使用`"|"`来表示保留换行，每行的缩进和行尾空白都会被去掉，额外的缩进会被保留。

使用`">"`来表示折叠换行，只有空白行才会被识别为换行，原来的换行符被转换成空格。

### 布尔值

true、True、TRUE、yes、Yes、YES皆为真。

false、False、FALSE、no、No、NO皆为假。

```yaml
boolean:
  - true
  - yes
  - false
  - no
```

### 整数

支持二进制表示。

```
int:
  - 666
  - 0001_0000
```

### 浮点数

支持科学计数法。

```yaml
float:
  - 3.14
  - 6.8523015e+5
```

### 空

null、Null、~、不指定默认值，都是空。

```yaml
nulls:
  - null
  - Null
  - ~
  -
```

### 时间戳

```yaml
date1: 2020-10-19
date2: 2020-10-19T01:00:00+08:00
date3: 2020-10-19T01:00:00.10+08:00
date4: 2020-10-19 01:00:00.10 +8
```

### 类型转换

使用 **双感叹号+目标类型** 来强制转换类型。

```yaml
a: !!float '666'
b: !!str 666
```

### 数据重用

```yaml
a: &anchor
  one: 1
  two: 2
  three: 3
b: *anchor
```

### 数据合并

使用`"<<"`可以与任意数据进行合并。

```yaml
human: &base
  body: 1
  hair: 999
singer:
  <<:
  skill: sing
programer:
  <<:
  hair: 6
  skill: code
```

