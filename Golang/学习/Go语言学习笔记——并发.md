# Go语言学习笔记——并发

[TOC]

&emsp;&emsp;并发是操作系统的主要特点之一，通过调度任务来获取CPU时间片实现并发

&emsp;&emsp;Go在语言层面实现了并发，有一个任务调度器用于调度任务，`goroutine`就是其中用来调度的任务

&emsp;&emsp;在Go中使用`go`关键字来启动一个`goroutine`，`goroutine`间用`channel`通信

### 使用goroutine

&emsp;&emsp;在调用方法/函数前，加上`go`关键字，即可启动一个`goroutine`，该方法的返回值会被省略，需要借助`channel`传递数据

```go
func foo(){
   fmt.Println("foo")
}

func main(){
   go foo()
   fmt.Println(321)
   time.Sleep(time.Second)
}
```

&emsp;&emsp;启动`goroutine`后，后面的代码不会等待前面的方法结束才开始执行，main()函数也不会等待`goroutine`的结束

&emsp;&emsp;启动`goroutine`后，就像其他语言中启动线程一样，无法预测程序的执行顺序

### 使用channel

&emsp;&emsp;多个`goroutine`间的通讯需要使用`channel`完成。

&emsp;&emsp;虽然操作内容也可以，但这样必须加锁，带来额外的开销，Go本身提倡使用`channel`通信

#### 创建channel

```go
c1 := make(chan int)
c2 := make(chan string)
```

&emsp;&emsp;`chan`后接的类型表示通道内的数据类型

&emsp;&emsp;通道内默认只能存一条数据，使用第二参数改变默认大小

```go
c1 := make(chan int, 5)
c2 := make(chan string, 5)
```

&emsp;&emsp;现在每个通道内能够存5条数据

&emsp;&emsp;如果缓冲区满了，写操作会阻塞；如果缓冲区是空的，读操作会阻塞

#### 使用channel

&emsp;&emsp;有了一个channel后，读写操作分别为

- 写操作 channel <- msg
- 读操作 msg := <-channel

```go
func foo(c chan string) {
   for msg := range c {
      fmt.Println(msg)
   }
}

func main() {
   c := make(chan string)
   go foo(c)
   c <- "hello"
   c <- "world"
}
```

&emsp;&emsp;在参数表中还可以定义通道读写权限

```go
func foo(c chan<- string)	//c在函数内只写
func foo(c <-chan string)	//c在函数内只读
func foo(c chan string)		//c在函数内可读可写
```

&emsp;&emsp;使用第二个左值判断通道是否关闭

```go
func foo(c chan string) {
   var msg string
   var ok bool
   msg, ok = <-c
   fmt.Println(msg, ok)
   msg, ok = <-c
   fmt.Println(msg, ok)
}

func main() {
   c := make(chan string, 5)
   go foo(c)
   c <- "hello"
   close(c)
}
```

&emsp;&emsp;通道关闭后，foo中不再阻塞，ok为false

### 使用select监听多个通道

&emsp;&emsp;在一个函数中同时处理多个通道的数据，如果按顺序写，前面的阻塞就会影响后面通道的接收

```go
func foo(c1,c2,c3 <-chan string) {
   var msg string
   for{
      msg = <- c1
      msg = <- c2
      msg = <- c3
   }
}
```

&emsp;&emsp;在这个程序中，任何一个通道阻塞，整个程序就堵塞了，其他通道来数据也无法及时响应

&emsp;&emsp;使用`select`改写

```go
func foo(c1, c2, c3 <-chan string) {
   var msg string
   for {
      select {
      case msg = <-c1:
         fmt.Println(msg)
      case msg = <-c2:
         fmt.Println(msg)
      case msg = <-c3:
         fmt.Println(msg)
      }
   }
}
```

&emsp;&emsp;任何一条通道来数据，都能及时响应

&emsp;&emsp;如果几个通道都是空的，依然阻塞，但也可以加上`default`处理，加了后就不再阻塞

### 同步

#### 竞态检测

&emsp;&emsp;Go提供的竞态检测可以发现程序中可能出现同步问题的地方

```go
var a = 0

func foo() {
   a++
}

func main() {
   for i := 0; i < 10; i++ {
      go foo()
   }
}
```

&emsp;&emsp;在终端中输入即可检查程序中是否有同步问题

```shell
go run -race main.go
```

&emsp;&emsp;结果显示foo()中a++会有竞争

#### 原子访问

&emsp;&emsp;使用atomic包提供的原子操作解决同步问题

```go
var a int32 = 0

func foo() {
   atomic.AddInt32(&a, 1)
}

func main() {
   for i := 0; i < 10; i++ {
      go foo()
   }
}
```

#### 互斥锁

&emsp;&emsp;为临界区代码加上锁，防止竞争

```go
var a int32 = 0
var lock sync.Mutex

func foo() {
   lock.Lock()
   defer lock.Unlock()
   a++
}

func main() {

   for i := 0; i < 10; i++ {
      go foo()
   }
}
```

#### 读写互斥锁

&emsp;&emsp;在读多写少的场景下，优先选择读写互斥锁`sync.RWMutex`

#### 等待组

&emsp;&emsp;等待组内有一个计数器，计数器的值可以通过方法调用进行增减，利用等待组内值不等于0阻塞的特点进行同步

&emsp;&emsp;`sync.WaitGroup`一共有三个方法

- Add(delta int) 计数器加delta，可以为负，但计数器值小于0后会宕机
- Done() 计数器减1
- Wait() 计数器不等于0时阻塞

