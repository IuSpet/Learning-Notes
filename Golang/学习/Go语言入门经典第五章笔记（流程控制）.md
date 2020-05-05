# Go语言入门经典第五章笔记（流程控制）

### if语句

```go
if flag != 3 {
   fmt.Println(i)
} else if i == 3 {
   fmt.Println("False")
} else {
   fmt.Println("True")
}
```

### switch语句

```go
switch i {
case 1:
   fmt.Println(1)
case 2:
   fmt.Println(2)
case 3:
   fmt.Println(3)
default:
   fmt.Println(4)
}
```

### for循环

```go
i := 2
for i < 10{
   i++
   fmt.Println(i)
}
```

```go
for i := 1; i < 10; i++ {
   i++
   fmt.Println(i)
}
```

```go
nums := []int{6, 5, 4, 3, 2, 1}
for i, n := range nums{
   //i是索引，n是值
   fmt.Println(i)
   fmt.Println(n)
}
```

### defer语句

&emsp;&emsp;`defer`语句让函数在返回前执行另一个函数

```go
func main() {
   defer fmt.Println("#########")
   fmt.Println("111111111")
}
```

&emsp;&emsp;实际执行顺序是先执行打印1，后打印

```go
func main() {
   defer fmt.Println("1111111111")
   defer fmt.Println("2222222222")
   defer fmt.Println("3333333333")
   fmt.Println("########")
}
```

​		多条`defer`语句，从后往前执行，即#答应后，按3、2、1的顺序打印