# Go语言学习笔记——接口

[TOC]

&emsp;&emsp;接口是一种提供给调用方和实现方遵守的协议约定，大家都按照统一的命名、参数表、返回值定义/使用实现了该接口的方法

&emsp;&emsp;Go的接口设计是非侵入式的，接口编写者无须知道接口被哪些类型实现；接口实现者只需知道实现的接口是什么样子，无须指明实现了哪一个接口。这种隐式的实现，解除了实现类与接口间的强耦合关系

### 声明接口

```go
type Invoke interface {
   Call(interface{}) int
}
```

&emsp;&emsp;在接口中，需要定义一个方法的完整签名，即名称、参数类型、返回值类型

### 实现接口

&emsp;&emsp;实现接口，必须以同样的签名定义内部的所有**方法**，方法的接收者可以是除了接口类型外的任意类型，包括函数

```go
type Invoke interface {
   Call(interface{})
}

type S struct{}

func (s *S) Call(p interface{}){
   fmt.Println("struct")
}

type FuncCaller func(interface{})

func (f FuncCaller) Call(p interface{}) {
   f(p)
}
```

&emsp;&emsp;类型S和函数FuncCaller都实现了Invoke接口

&emsp;&emsp;类型和接口间是多对多的关系，**一个类型可以实现多个接口，一个接口也可以由多个类型实现**

&emsp;&emsp;对于一个结构体类型，如果它**内部的字段实现了一个接口的所有方法**，该接口体类型也实现了该接口

```go
type Caller interface {
   call1()
   call2()
}

type A struct {}
type B struct {}

type S struct {
   A
   B
   int
}

func (a A) call1(){
   fmt.Println(123)
}

func (b B) call2(){
   fmt.Println(321)
}

func main() {
   var tmp Caller
   tmp = new(S)
   tmp.call1()	//123
   tmp.call2()	//321
}
```

&emsp;&emsp;在这段程序中，虽然结构体S没有作为接收器实现任何方法，但是A、B实现了Caller接口的方法，所以S也是实现类Caller的一个类型

### 接口嵌套

&emsp;&emsp;Go中，不仅结构体可以嵌套，接口也可嵌套组合为新的接口

```go
type Input interface {
   read([]byte) int
}

type Output interface {
   write([]byte) int
}

type IO interface {
   Input
   Output
}
```

&emsp;&emsp;接口IO中嵌套了Input和Output接口

```go
type S struct{}

func (s *S) read([]byte) int{
   return 1
}

func (s *S) write([]byte) int{
   return 0
}
```

&emsp;&emsp;如果一个类型实现了IO中的所有接口，那么它其实算实现了全部三个接口

### 接口和类型转换

&emsp;&emsp;Go中的接口也可以像其他语言中，作为实现了自身接口的类型的类型使用

```go
var a Invoke
//如果接收器是传值，两种赋值都行
a = S{}
//如果接收器传指针，只能赋值指针
a = &S{}
```

&emsp;&emsp;因为Go中的语法糖，指针用`.`运算符调用方法，会自动解引再调用，所以左边是指针时，既可以调用接收器是指针的方法，也可以调用接收器是实例的方法；但对于实例，没有自动取指针再调用的转换，所以对于接收器是指针的方法，无法赋值实例去做之后的调用

#### 接口断言

一个实例可以任意转换为各种接口类型

```go
var io IO = new(S)
a := io.(Input)
b := io.(Output)
fmt.Printf("%p\t%p\t%p\n", io, a, b)	//三个值相同，指向同一内存的指针
```

&emsp;&emsp;但如果该实例的类型没有实现该接口，在编译阶段不会产生错误，但运行时会引发宕机

&emsp;&emsp;可以加上另一个返回值，代表是否成功转换

```go
type T interface {
	call()
}
......
var io IO = new(S)
a, ok := io.(T)
fmt.Println(a, ok)	//<nil> false
```

&emsp;&emsp;接口类型也可以转换回普通类型

```go
var io IO = new(S)
a := io.(*S)
fmt.Println(reflect.TypeOf(a))	//*main.S
fmt.Printf("%p\t%p\n", io, a)		//io与a的值相同
```

### 空接口类型interface{}

&emsp;&emsp;空接口是接口类型的特殊形式，空接口没有任何方法，任何类型都无须实现该接口，但反过来说任何类型也都满足空接口，所以空接口可以接收任何类型

&emsp;&emsp;空接口的内部实现保存了对象的类型和指针。**使用空接口保存一个数据会比直接用数据对应类型要慢**，因此必要时才使用空接口

#### 空接口与其他类型转换

&emsp;&emsp;所有类型都可以直接转换为空接口，空接口类型转换为其他类型需要接口断言

```go
a := 5
var i interface{}
i = a
b := i.(int)
```

#### 空接口比较

- 实际类型不同，结果为false
- 不能比较引用，如切片，映射
- 数组、函数、通道可以比较

### 类型分支

&emsp;&emsp;一个接口变量的实际类型可能很多，可以用`switch`语句进行判断

```go
var a interface{}
switch a.(type) {
case int:
   fmt.Println("int")
case float64:
   fmt.Println("float64")
case string:
   fmt.Println("string")
default:
   fmt.Println("other")
}
```

### 参考资料

《Go语言从入门到进阶实战》第7章