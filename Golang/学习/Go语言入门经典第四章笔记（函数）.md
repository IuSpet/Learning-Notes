# Go语言入门经典第四章笔记（函数）

### 函数结构

```go
func add(x int, y int) int{
	return x + y;
}
```

​		使用 `func` 关键字声明函数，然后是函数名、参数表、返回类型。结构类似使用标注声明的python函数

### 返回多个值

```go
func foo() (int, string) {
	return 2, "hello"
}
//使用时
a,s := foo()
```

### 不定参数函数

```go
func addAll(numbers...int) int{
   total := 0
   for _, number := range numbers{
      total += number
   }
   return total
}
//使用时
ans := addAll(1,2,3,4,5)
```

### 使用具名返回值

&emsp;&emsp;为返回值添加标识符，在函数中赋值，提升代码可读性。为具名变量赋值后，返回时无须显式地返回相应的变量

```go
func hello() (x, y string) {
   x = "hello"
   y = "world"
   return
}
```

### 将函数作为值传递

&emsp;&emsp;类似于python，函数也被视为一种类型，可以将函数赋值给一个变量

```go
package main

import (
   "fmt"
)

func world() string {
   return "world"
}

func hello(f func() string) string {
   return "hello " + f()
}

func main() {
   	tmp := hello
	fmt.Println(tmp(world))
}
```