# Go语言学习笔记——反射

[TOC]

&emsp;&emsp;反射是指在程序运行期间对程序本身进行访问和修改的能力

&emsp;&emsp;举例来说，一个函数接收了interface{}类型的变量，传入函数之前，这个变量肯定是在程序某处定义过的，虽然我们看这个函数无法得知传进来的具体是什么，但是程序自身在运行时，肯定知道这个变量的具体信息，而反射就是获取这些信息的操作

&emsp;&emsp;Go使用reflect包访问程序的反射信息

### 获取类型信息

```go
func foo(x interface{}) {
   fmt.Println(reflect.TypeOf(x))
}

func main() {
   var a int64 = 20
   var b float32 = 3.14
   var c = []string{"hello"}
   foo(a)
   foo(b)
   foo(c)
   foo(struct {
      int
      bool
   }{5, true})
}
```

打印结果

```
int64
float32
[]string
struct { int; bool }
```

### 获取种类定义

&emsp;&emsp;对于结构体来说，不同的结构体算的类型，但本质上都是结构体

```go
tp := reflect.TypeOf(x)
fmt.Println(tp.Kind())
```

&emsp;&emsp;对类型调用`Kind()`方法即可得到种类信息

### 获取指针指向对象类型

```go
tp := reflect.TypeOf(x)
fmt.Println(tp.Elem())
```

&emsp;&emsp;对类型调用`Elem()`方法即可得到指针指向的对象的类型

### 获取获得结构体的字段信息

```go
func foo(x interface{}) {
   tp := reflect.TypeOf(x)
   filedNum := tp.NumField()
   for i := 0; i < filedNum; i++ {
      fmt.Println(tp.Field(i))
   }
}
func main() {
   foo(struct {
      a int
      b float32
      c bool
   }{2, 2,true})
}
```

```
{a main int  0 [0] false}
{b main float32  8 [1] false}
{c main bool  12 [2] false}
```

&emsp;&emsp;每个字段的信息包括5个内容

&emsp;&emsp;{字段名 字段路径 字段类型 字段偏移 字段索引 是否为匿名字段}

### 获取值

```go
func foo(x interface{}) {
   fmt.Println(reflect.ValueOf(x))
}
```

### 利用类型信息创建实例

```go
tp := reflect.TypeOf(x)
y := reflect.New(tp)
y.Elem().SetInt(10)
fmt.Println(y.Elem())	//10
```

### 反射调用函数

1. 使用`reflect.ValueOf()`获得函数
2. 将参数构造为`[]reflect.Value`
3. 调用函数，用`[]reflect.Value`接收返回值

```go
func foo(x interface{}) {
   fc := reflect.ValueOf(x)
   var params = []reflect.Value{reflect.ValueOf(10)}
   res := fc.Call(params)
   fmt.Println(res[0].Int())
}
func main() {
   foo(func(a int) int { fmt.Println(a * a); return 5 })
}
```