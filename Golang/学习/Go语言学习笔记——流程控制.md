# Go语言学习笔记——流程控制

[TOC]

## 分支

### if语句

&emsp;&emsp;一般用法与C语言类似，但是判断表达式不用加括号

```go
var a int
if a < 4 {
   a++
} else if a == 4 {
   a--
} else {
   a *= 2
}
```

#### 特殊写法

&emsp;&emsp;在判断条件表达式前，可以加额外的语句

```go
if b := a * 2; b < 10{
   fmt.Println(b)
}
```

&emsp;&emsp;这样写的好处是对于块作用域，变量作用的范围更小

### switch语句

&emsp;&emsp;在Go中，`switch case`语句的条件比C更加灵活方便，case值不局限于整数

```go
var a int
switch a{
case 2:
   fmt.Println(a)
case 3, 4, 5, 6, 7:
   fmt.Println(a)
default:
   fmt.Println(a)
}
```

&emsp;&emsp;Go中不需要用`break`说明一个分支结束，如果多个分支使用同一处理，可以写在一个表达式中

&emsp;&emsp;还可以将多个`if else`的结构写成`swich`语句

```go
var a int
switch {
case a < 10:
   fmt.Println(a)
case a > 10 && a < 20, a > 50 && a < 60:
   fmt.Println(a)
default:
   fmt.Println(a)
}
```

## 循环

### for循环

&emsp;&emsp;Go中没有`while`关键字，只有`for`

&emsp;&emsp;几种常用写法

```go
var a int
//死循环
for {
   a++
}
//判断条件循环
for a < 10 {
   fmt.Println(a)
}
//固定次数循环
for i := 0; i < 10; i++ {
   fmt.Println(i)
}
```

&emsp;&emsp;`break`和`continue`关键字功能与其他语言相同

&emsp;&emsp;`goto`、`return`、`panic`语句也可以推出循环

### for range循环

&emsp;&emsp;对可迭代数据的循环，类似于C++的`for( : )`，Python的`for in`

#### 遍历切片、数组、字符串

&emsp;&emsp;单个左值是索引，两个左值分别是索引和值

```go
for index, value := range s {
   fmt.Println(index, value)
}
```

#### 遍历map

&emsp;&emsp;单个左值是key，两个分别是key和value

```go
var m = map[string]int{
   "hello": 123,
   "world": 321,
}
for key := range m {
   fmt.Println(key)
}
for key, value := range m {
   fmt.Println(key, value)
}
```

#### 遍历通道

```go
c := make(chan int)
for msg := range c{
   fmt.Println(msg)
}
```

&emsp;&emsp;从通道中不断获取数据

## 跳转

&emsp;&emsp;使用标签标记代码块，再配合使用`goto`、`break`、`continue`进行跳转

#### break label

```go
loop:
   for {
      for{
         break loop
      }
      fmt.Println("outer loop")
   }
   fmt.Println("outside")
//结果只打印 outside
```

&emsp;&emsp;使用break label，直接跳出两层循环

#### continue label

```go
loop:
   for {
      fmt.Println("outer loop")
      for{
         continue loop
      }
   }
//结果一直打印 outer loop
```

&emsp;&emsp;使用continue label，回到外层循环开头

&emsp;&emsp;**break和continue后面接label只能接定义在`for`、`switch`、`select`代码块前一行的lebel**

#### goto label

&emsp;&emsp;goto是无条件跳转，可以跳转到任意label，高级语言中不推荐使用

```go
	 for {
      for {
         goto exit
      }
   }
exit:
   fmt.Println("outside")
```

## 参考资料

《Go语言从入门到进阶实战》第四章