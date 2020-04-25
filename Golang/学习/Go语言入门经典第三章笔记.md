# Go语言入门经典第三章笔记（变量）

[TOC]

### 声明变量

```go
//声明变量
var s1 string
//声明变量并赋值
var s2 string = "foo"
//一次声明多个对象并赋值
var s3, s4 string = "foo","bar"
//省略变量类型但必须赋值
var s5 = "bar"
//一次声明多个不同类型变量并赋值
var (
   s5 string = "foo"
   n1 int32 = 8
)
```

### 变量默认值

&emsp;&emsp;在Go中，只声明未赋值的变量，等于默认零值

```go
var s string		//""
var i int			//0
var f float64		//0
var b bool			//false
```

### 简短变量声明

```go
s := "foo"
i := 2
f := 3.14
b := false
```

&emsp;&emsp;编译器根据赋值自动推断变量类型，所以必须赋值初始化，**只能在函数中使用简短变量声明**，这也是与 `var val = 2` 的区别

### 常用风格

- 在函数内使用简短变量声明，`i := 2`
- 在函数外省略类型，`var i = 2`

&emsp;&emsp;使用这两种方式的前提自然是需要在声明变量的同时赋值

### 变量作用域

​		Go使用基于块的词法作用域

### 使用指针

​		与C相同

### 声明常量

&emsp;&emsp;用 `const` 关键字代替 `var` 

```go
const a = 2
const s string = "hello"
```