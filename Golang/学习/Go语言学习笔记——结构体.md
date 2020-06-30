# Go语言学习笔记——结构体

### 定义结构体

&emsp;&emsp;如果字段类型不重复，可以直接写字段类型，省略字段名，称为类型内嵌或匿名字段

```go
type S1 struct {
   int
   string
   float32
}
```

&emsp;&emsp;使用时将自动类型当字段名用

&emsp;&emsp;如果有重复字段，可以用别名替换后省略字段名，或者加上字段名

```go
type S1 struct {
   a int
   b int
   c string
}
```

### 实例化结构体

```go
var s1 S
s2 := new(S)
s3 := &S{}
s4 := S{}
```

&emsp;&emsp;其中s1、s4是实例，s2、s3是实例的指针

&emsp;&emsp;不同于C，在Go中，指针也可以用`.`运算符去访问字段

### 初始化结构体

```go
var s1 = S{1, 2, "a"}
s2 := new(S)
s3 := &S{2, 1, "b"}
s4 := S{2, 2, "c"}
s5 := struct {
   int
   bool
}{5, true}
```

&emsp;&emsp;其中s5是定义并初始化一个匿名结构体

&emsp;&emsp;也可以在初始化时写上字段名

```go
var s1 = S{
   a:1, 
   b:2, 
   c:"a"}
```

### 结构体标签

&emsp;&emsp;在结构体可以为字段添加标签，用于json传输等场合

```go
type NetConf struct {
    Master string `json:"master"`
    Mode   string `json:"mode"`
    MTU    int    `json:"mtu"`
    Debug  bool   `json:"debug"`
}
```

&emsp;&emsp;标签中的json信息，会在序列化时命名对应字段，如果有同名json标签，**序列化时会忽略重复的**

&emsp;&emsp;除了json，在使用gorm时，结构体标签还可以描述该字段在数据库中的信息

### 使用结构体

&emsp;&emsp;可以为将定义的结构体作为接收器定义方法

&emsp;&emsp;如果结构体内的字段具有唯一签名的方法，调用时可以省略字段

```go
type X struct {}
type Y struct {}

func (x X) foo() {
   fmt.Println("x")
}

func (y Y) bar(){
   fmt.Println("Y")
}

type S struct {
   X
   Y
}

func main() {
   var s S
   s.foo()
   s.bar()
}
```

&emsp;&emsp;实际上是字段X调用了foo()，字段Y调用了bar()，但如果签名相同，则必须显式的调用

```go
type X struct {}
type Y struct {}

func (x X) foo() {
   fmt.Println("x")
}

func (y Y) foo(){
   fmt.Println("Y")
}

type S struct {
   X
   Y
}

func main() {
   var s S
   s.X.foo()
   s.Y.foo()
}
```