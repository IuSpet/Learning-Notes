# Go语言学习笔记——函数

[TOC]

&emsp;&emsp;Go支持普通函数、匿名函数和闭包

&emsp;&emsp;Go中的函数是一等公民，可以像其他类型一样使用，赋值、传递参数、接收方法等

### 普通函数声明

```
func 函数名(参数列表) (返回参数列表){
		函数体
}
```

&emsp;&emsp;一个包内，函数名称不能重名

```go
func foo(a, b string) (string, bool) {
   return a + b, true
}
```

&emsp;&emsp;同一类型参数可以简写，返回值可以有多个

### 带变量名的返回值

&emsp;&emsp;声明函数时可以给返回值变量名，在函数体内可以作为变量使用，return语句后不用写要返回的内容

```go
func foo(a, b string) (c string) {
   c = a + b
   return
}
```

&emsp;&emsp;如果返回值带变量名，必须所有返回值都有变量名，混用无法通过编译

### 函数变量

&emsp;&emsp;Go中，函数也是一种类型，可以赋值和传递

```go
func foo(a, b string) (c string) {
   c = a + b
   return
}

func main() {
   var f func(string, string) string
   f = foo
   f2 := foo
   fmt.Println(f, f2)
}
```

&emsp;&emsp;函数变量的类型要写明函数的参数类型和返回参数类型，或者用简短变量，打印结果是2个相同地址，猜测是函数入口地址

### 匿名函数

&emsp;&emsp;匿名函数类似Java中的匿名类，不同的是匿名类是匿名实现接口，Go中是直接匿名定义函数

&emsp;&emsp;Go中定义匿名函数，缺省函数名，其他与普通函数相同，一般有以下用法

#### 定义时调用

```
func(参数表)返回类型{
	函数体
}(调用执行参数)
```

```go
func(s string) {
   fmt.Println(s)
}("hello world")
tmp := func(a, b int) int {
   return a + b
}(2, 3)
fmt.Println(tmp)
//hello world
//5
```

#### 将匿名函数赋值给函数变量

```go
tmp := func(a, b int) int {
   return a + b
}
```

#### 作为回调函数

```go
func foo(a []int, f func(int) int) int {
   var sum int
   for _, v := range a {
      sum += f(v)
   }
   return sum
}

func main() {
   num := []int{1, 2, 3, 4, 5}
   ans := foo(num, func(i int) int {
      return i * i
   })
   fmt.Println(ans)
}
```

&emsp;&emsp;可以回调不同的函数对切片值做不同的操作后求和

### 可变参数函数

```go
func foo(x ...int) {
   fmt.Println(reflect.TypeOf(x))
}
```

&emsp;&emsp;在函数参数表中，类型前加`...`即表示该参数接受可变参数，在函数体内会自动组装为一个切片使用

&emsp;&emsp;调用时，既可以传入多个参数，也可以传入切片后加`...`，不能这样传数组

```go
foo(1, 2, 3, 4, 5)
var a = []int{1, 2, 3, 4, 5}
foo(a...)
```

&emsp;&emsp;打印地址可以发现传的是切片的引用值，也就是数据所在的实际地址

`interface{}`代表任意类型，如果函数定义为

```go
func foo(x ...interface{}){......}
```

&emsp;&emsp;就可以传入任意数量任意类型的参数

### defer语句

&emsp;&emsp;`defer`语句后的内容，会在函数结束前调用，函数可以是正常结束，也可以是宕机时

```go
func main() {
   defer fmt.Println("11111")
   defer fmt.Println("22222")
   defer fmt.Println("33333")
   fmt.Println("hello world")
}
```

&emsp;&emsp;执行结果为

```
hello world
33333
22222
11111
```

&emsp;&emsp;多个`defer`语句，调用顺序是先定义后调用，这也符合申请释放资源的顺序，即后申请的资源需要先释放

&emsp;&emsp;依靠`defer`语句的特点，可以完成其他语言中finally块的内容，即释放资源的工作，如关闭文件、关闭Socket等

### 方法

&emsp;&emsp;Go中没有类的概念，自然也不会在类中定义函数作为类的方法使用

Go中定义方法

```go
type MyInt int

func (x *MyInt) Increase() {
   *x++
}
```

&emsp;&emsp;函数名前是一个接收器，代表了可以调用该方法的对象类型，同时会通过传值进入方法，所以想要修改自身，需要传递指针进来

&emsp;&emsp;接收器可以是任意的定义在当前包中的自定义类型，即用type声明的类型

&emsp;&emsp;用接收器对象调用执行

```go
var v MyInt
v = 5
v.Increase()
fmt.Println(v)	//6
```