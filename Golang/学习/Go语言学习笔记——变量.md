# Go语言学习笔记——变量与常量

[TOC]

## 变量

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

&emsp;&emsp;如果表达式内带有类型信息，编译器还是会根据表达式的类型进行推断

```go
var b = float32(3.14)
fmt.Println(reflect.TypeOf(b))
//float32
```

##### 短变量声明

&emsp;&emsp;在函数体内，还可以省略var写出更精简的变量声明形式

```go
a := 2	//等价于 var a int = 2
b := 3.14 //等价于 var b float64 = 3.14
```

&emsp;&emsp;对于函数的返回值，这种形式非常方便

```go
conn, err := net.Dial("tcp", "127.0.0.1:8080")
//等价于
var conn net.Conn
var err error
conn, err = net.Dial("tcp", "127.0.0.1:8080")
```

### 多个变量赋值

&emsp;&emsp;使用逗号隔开，可以一次对多个变量赋值

```go
a, b, c := 1, 2, 3
a, b, c = 3, 2, 1
a, b, c = c, a, b
```

### 匿名变量

&emsp;&emsp;在go中，声明而未使用的变量会导致编译报错。如果需要接受函数的返回值，但又不会继续使用，可以使用匿名变量，即 `_` 

```go
a := []int{1, 2, 3, 4, 5}
for _, v := range a {
   fmt.Println(v)
}
```

&emsp;&emsp;这里循环内不需要a的索引，所以使用匿名变量，编译也能通过

## 常量

&emsp;&emsp;常量在编译期计算确定，运行期间不能改变

#### 常量声明

&emsp;&emsp;将声明变量时的`var`改为`const`

```go
const pi float64 = 3.1415926
const e = 2.71828
const (
   a = 5
   b = "hello"
)
```

&emsp;&emsp;因为常量在编译期确定，所以可以用于数组声明，使用变量会报错

```go
const len = 5
var arr [len]int
```

#### 枚举

&emsp;&emsp;Go中暂时没有枚举，可以使用常量配合`iota`模拟枚举

```go
type weekday int
const (
   mon weekday = iota + 1
   tue
   wed
   thu
   fri
)
var d weekday = wed
fmt.Println(d)
```

&emsp;&emsp;也可以利用`iota`的递增性质去构造其他枚举，如奇数、偶数、数位

&emsp;&emsp;为`weekday`绑定方法后利用`switch case` 语句可以枚举更多信息

```go
type weekday int
const (
   mon weekday = iota + 1
   tue
   wed
   thu
   fri
)
func(day weekday) String() string{
   switch day {
   case mon:
      return "星期一"
   case tue:
      return "星期二"
   case wed:
      return "星期三"
   case thu:
      return "星期四"
   case fri:
      return "星期五"
   }
   return "error"
}
```

## 类型别名与类型定义

### 类型别名

写法：`type <new_name> = <origin_name>`

&emsp;&emsp;为一个类型取一个新的名字，编译时仍作为原类型处理

```go
type weekday = int
func fun(day weekday) int{
	return day
}
```

&emsp;&emsp;编译没有问题，weekday被当作int处理

### 类型定义

写法：`type <new_type> <origin_type>`

&emsp;&emsp;定义一个新的类型，与原类型分开处理，两种类型的变量不能混用

```go
type weekday int
func fun(day weekday) int{
	return day
}
```

&emsp;&emsp;编译会报错，因为返回值day的类型是weekday，与函数定义的int不符

#### 为非本地类型定义方法

```go
type dur = time.Duration
func (d dur) fun() {}		//编译出错
```

&emsp;&emsp;`time.Duration`是包外定义的类型，在自己的包中不能为它定义方法，使用类型别名会编译报错

&emsp;&emsp;此时可以利用类型定义，指定新的类型定义方法，但是新的类型不能使用原类型的方法

```go
type dur time.Duration
func (d dur) fun() {}		//编译通过
```

#### 在结构体中使用别名

在go中声明结构体如果类型不同可以不用变量名，但是相同的类型名会产生歧义，可以使用别名替代

```go
type a struct {
   int
   int
}
//替换为
type fakeInt = int
type b struct {
   int
   fakeInt
}
```

## 参考资料

《Go语言从入门到实践》第二章