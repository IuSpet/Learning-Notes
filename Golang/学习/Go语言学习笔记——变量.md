# Go语言学习笔记——变量

### 声明变量

#### 标准格式

&emsp;&emsp;`var 变量名 变量类型`

```go
//整型变量
var a int
//64位浮点数组变量
var b []float64
//参数为空，返回值是bool的函数变量
var c func() bool
//结构体变量
var e struct {
   x int
}
```

#### 一次声明多个变量

```go
//声明同种变量
var a,b,c int
//声明不同种变量
var(
   x float64
   y string
   z bool
)
```

### 初始化变量

#### 默认值

&emsp;&emsp;如果使用前面的形式声明变量，那么新声明的变量的值为默认值

```go
var s string		//""
var i int			//0
var f float64		//0
var b bool			//false
//切片、函数、指针等的默认值为nil
```

#### 声明时初始化

##### 标准格式

&emsp;&emsp;`var 变量名 类型 = 表达式`

```go
var a int = 5
var b float64 = 3.14
var c string = "hello"
```

##### 省略类型

&emsp;&emsp;既然给了变量初始值，那么变量的类型也肯定与初始值相同，可以省略类型，交给编译器去根据表达式推断

&emsp;&emsp;`var 变量名 = 表达式`

```go
var a = 5
var b = 3.14
var c = "hello"
```

&emsp;&emsp;如果表达式是浮点数常量，编译器会推断为float64类型，提高精度

```go
var b = 3.14
fmt.Println(reflect.TypeOf(b))
//float64
```

​		如果表达式内带有类型信息，编译器还是会根据表达式的类型进行推断

```go
var b = float32(3.14)
fmt.Println(reflect.TypeOf(b))
//float32
```