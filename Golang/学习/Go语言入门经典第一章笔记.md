# Go语言入门经典第一章笔记

### Go简介

&emsp;&emsp;Golang是Google在2007年开发的一种开源编程语言，Go的目标是：兼具Python等动态语言的开发速度和C\C++等编译型语言的性能与安全性

### HelloWorld程序

创建文件 `hello.go`

```
package main

import "fmt"

func main() {
	fmt.Println("Hello World!")
}
```

&emsp;&emsp;在终端中输入 `go run hello.go` 编译并运行

&emsp;&emsp;或者输入 `go build hello.go` 编译生成可执行文件，然后再运行可执行文件，使用 `go clean` 可以清除编译生成的可执行文件

### 练习题

1. go build 和 go run 的不同之处

&emsp;&emsp;go build执行编译，生成一个可执行的二进制文件，这个文件可用来运行程序；go run编译并运行程序