# Go语言学习笔记——函数闭包

### 定义

> 在计算机科学中，闭包（英语：Closure），又称词法闭包（Lexical Closure）或函数闭包（function closures），是在支持头等函数的编程语言中实现词法绑定的一种技术。**闭包在实现上是一个结构体，它存储了一个函数（通常是其入口地址）和一个关联的环境（相当于一个符号查找表）**。环境里是若干对符号和值的对应关系，它既要包括约束变量（该函数内部绑定的符号），也要包括自由变量（在函数外部定义但在函数内被引用），有些函数也可能没有自由变量。闭包跟函数最大的不同在于，当捕捉闭包的时候，它的自由变量会在捕捉时被确定，这样即便脱离了捕捉时的上下文，它也能照常运行。捕捉时对于值的处理可以是值拷贝，也可以是名称引用，这通常由语言设计者决定，也可能由用户自行指定（如C++）。

&emsp;&emsp;所以函数闭包的实际含义就是一个函数与引用环境的关联

&emsp;&emsp;~~以前看到闭包想到的都是离散里的闭包0.0~~

### 实际使用

```go
func foo() func() int{
   var x int
   return func() int {
      x++
      return x
   }
}
```

&emsp;&emsp;对于该函数，如果直接调用

```go
fmt.Println(foo()())
fmt.Println(foo()())
fmt.Println(foo()())
```

&emsp;&emsp;无论调用几次，打印结果都是1

&emsp;&emsp;但是换一个方法后

```go
f1 := foo()
f2 := foo()
fmt.Println(f1())	//1
fmt.Println(f1())	//2
fmt.Println(f1())	//3
fmt.Println(f2())	//1
fmt.Println(f2())	//2
fmt.Printf("%p\n",f1)	//0x109eea0
fmt.Printf("%p\n",f2)	//0x109eea0
```

&emsp;&emsp;再次打印x的地址，发现对于f1和f2，它们调用时，x的地址不同

```
xptr: 0xc000014090
1
xptr: 0xc000014090
2
xptr: 0xc000014090
3
xptr: 0xc000014098
1
xptr: 0xc000014098
2
```

&emsp;&emsp;分析整个程序，f1调用foo()时，在foo中创建了一个变量x，在返回的函数中，含有x的引用，也就是foo函数调用结束了，但x留了下来，f1掌握者它的引用

&emsp;&emsp;f2在调用foo()时，也在foo中创建了一个变量x，同时也随着返回函数保留了下来，

&emsp;&emsp;再回过头看函数闭包的定义，x就是被捕获的自由变量，而f1和f2就是两个不同的函数闭包

