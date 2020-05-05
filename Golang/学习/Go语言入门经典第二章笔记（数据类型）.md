# Go语言入门经典第二章笔记（数据类型）

### Go是静态类型语言

​		Go语言是静态类型语言，在定义函数时，需要声明参数和返回值的类型

```go
func sayHello(s string) string {
	return "hello " + s
}
```

### 布尔类型

```go
var b bool
```

默认值是 false

### 数值类型

#### 整型

```go
var num int = 123
```

#### 浮点数

```go
var f1 float32 = 3.14
var f2 float64 = 2.718
```

### 字符串

```go
var str string = "foo"
```

### 数组

```go
var nums [4]int
var strs [5]string
```

### 获取变量类型

使用 `reflect.TypeOf()` 获取一个变量的实际类型

```go
var nums [4]int
fmt.Println(reflect.TypeOf(nums))
//[4]int
```

### 类型转换

使用strconv包提供的字符串与其他类型的转换方法

```go
var s string = "F"
b, _ := strconv.ParseBool(s)
fmt.Println(b)
//false
fmt.Println(reflect.TypeOf(b))
//bool
//从其他类型转换为字符串
var num int64 = 123
var str string = strconv.FormatInt(num,10)
```

