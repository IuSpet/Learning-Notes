# Go语言学习笔记——容器

[TOC]

## 数组

&emsp;&emsp;长度固定的连续内存区域

### 声明数组

`var <标识符> [size]T`

&emsp;&emsp;其中，size必须是一个在编译期能确定的值，不能在运行期间才能计算确定，如果为空则是一个切片

```go
var a [2]int
var b [4]float64
var c [5][5]string
```

### 初始化数组

```go
var a = [5]int{1,2,3}
```

&emsp;&emsp;如果不确定数组长度，可以写成

```go
var a = [...]int{1,2,3,4,5,6}
```

&emsp;&emsp;由编译器确定长度，不能像C语言写成缺省的形式，缺省会创建一个**切片**而非数组

### 遍历数组

&emsp;&emsp;利用`for range`循环遍历数组

------

## 切片

&emsp;&emsp;大小可变的数组，是一个引用

```go
a := make([]int, 1)
fmt.Printf("%p\t%p\n", &a, a)
a = append(a, 1, 2, 3, 4)
fmt.Printf("%p\t%p\n", &a, a)
//0xc00000c060    0xc000014090
//0xc00000c060    0xc00001c120
```

&emsp;&emsp;a存的是一个数组的地址，当数组容量不足时，开辟一块新的内存，并将新地址存到a，所以切片是一个引用

### 对已有数组切片

```go
var a = [10]int{1, 2, 3, 4, 5, 6}
b := a[2:5]
```

&emsp;&emsp;范围左闭右开，不能是负数也不能超出数组范围

&emsp;&emsp;切片是一个引用，所以修改原数组的内容，切片中的内容会对应改变

### 声明切片

&emsp;&emsp;缺省数组大小的方式声明

```go
var a []int
```

&emsp;&emsp;利用`make()`声明

```go
a := make([]int, 5)
b := make([]int, 5, 10)
```

&emsp;&emsp;其中第一个参数指类型，第二个参数是切片大小，第三个参数是切片容量，缺省默认等于切片大小

&emsp;&emsp;使用`len()`计算两个切片的长度都为5

### 复制切片

&emsp;&emsp;使用`copy(dst []Type, src []Type)`函数完成深拷贝，`copy()`返回拷贝的元素数量

```go
a := make([]int, 5)
b := make([]int, 5, 10)
copy(b, a)
```

### 添加和删除元素

&emsp;&emsp;使用`append()`对切片中的元素进行添加和删除

#### 添加元素

&emsp;&emsp;可以一次只添加一个元素，也可以添加一个切片，如果切片容量不够，会**扩容为原来2倍大小**，扩容后切片指向的地址也会发生改变

```go
a := make([]int, 5)
b := make([]int, 5, 10)
a = append(a, 2)
b = append(b,a...)
```

#### 删除元素

```go
a := make([]int, 5)
for i,_ := range a{
   a[i] = i+1
}
a = append(a[:2],a[2+1:]...)
fmt.Println(a)	//[1 2 4 5]
//删除了a[2]
```

------

## 映射

&emsp;&emsp;Go提供的映射容器为map，使用散列表实现

### 使用映射

```go
m := make(map[string]int)
m["hello"] = 2
m["world"] = 5
//单个左值返回映射值
a := m["hello"]
//两个左值，一个返回映射值，一个返回是否有该key
b, c := m["abc"]
```

&emsp;&emsp;另一种创建映射的方式

```go
m := map[string]int{
   "hello": 5,
   "world": 10,
}
```

### 遍历映射

&emsp;&emsp;使用`for range`迭代遍历，左值为key,value，如果只写一个，则是key

### 删除映射表中的键值对

`delete(map,key)`

```go
m := map[string]int{
   "hello": 5,
   "world": 10,
}
delete(m,"hello")
```

&emsp;&emsp;如果映射中没有该键，不做任何操作

### 清空映射

&emsp;&emsp;重新为标识符创建一个新映射，原来的映射会沦为垃圾内存，交给垃圾回收处理

### 线程安全的映射

`sync.Map`

### 使用sync.Map

&emsp;&emsp;声明时无需指定键值类型

```go
var m sync.Map
```

#### 读写删除操作

&emsp;&emsp;可以插入键值类型不同的键值对

```go
var m sync.Map
//用Store方法写
m.Store("hello",123)
m.Store(123,"world")
m.Store(3.14,true)
//用Load方法读，第二个返回值代表是否存在
a, _ := m.Load("hello")
b, _ := m.Load(123)
c, _ := m.Load(3.14)
//用Delete方法删除，参数是key
m.Delete(123)
m.Delete("hello")
m.Delete(3.14)
```

#### 遍历sync.Map

&emsp;&emsp;使用方法`Range(func)` 传入回调函数处理，返回值是true继续迭代，false停止迭代

```go
m.Range(func(key, value interface{}) bool {
   fmt.Println(key, value)
   return true
})
```

------

## 列表

&emsp;&emsp;列表是链表容器

### 初始化列表

```go
l1 := list.New()
var l2 list.List
```

### 在列表中插入元素，删除元素

```go
//从后插入
l1.PushBack("123")
//从头插入
e := l1.PushFront("321")
//从指定位置后插入
l1.InsertAfter(123,e)
//从指定位置前插入
l1.InsertBefore(3.14,e)
//删除元素
l1.Remove(e)
```

&emsp;&emsp;还可以从首尾插入其他列表

### 遍历列表

&emsp;&emsp;使用`for`循环遍历，类似C++中容器的迭代器循环

```go
for i := l1.Front(); i != nil; i = i.Next(){
   fmt.Println(i.Value)
}
```

