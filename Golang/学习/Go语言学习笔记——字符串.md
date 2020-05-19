# Go语言学习笔记——字符串

[TOC]

### 声明字符串

&emsp;&emsp;字符串是Go中的基本类型，声明时与其他基本类型一致

```go
var s1 string = "hello"
var s2 = "world"
s3 := "abc"
```

### 计算字符串长度

&emsp;&emsp;使用Go内置函数`len()`计算长度，返回值为存储字符串所用的字节数

```go
var s1 string = "hello"
var s2 = "world"
s3 := "你好鸭"
fmt.Println(len(s1), len(s2), len(s3))	//5 5 9
```

&emsp;&emsp;Go采用UTF-8编码存储字符串，ascii字母使用一个字节，中文字符使用三个字节

&emsp;&emsp;使用`utf8`包提供的函数计算Unicode字符数量

```go
var s1 string = "hello"
var s2 = "world"
s3 := "你好鸭"
fmt.Println(utf8.RuneCountInString(s1))	//5
fmt.Println(utf8.RuneCountInString(s2))	//5
fmt.Println(utf8.RuneCountInString(s3))	//3
```

### 遍历字符串

#### 遍历字符串每个字节

&emsp;&emsp;获取字符串字节长度后使用for循环遍历

```go
var s1 string = "hello世界"
for i := 0; i < len(s1); i++ {
   fmt.Printf("%c\n", s1[i])
}
```

&emsp;&emsp;对于ascii字符可以正常打印，但对于中文会打印乱码

#### 遍历每个Unicode字符

&emsp;&emsp;使用`for range`进行迭代循环

```go
var s1 string = "hello世界"
for _, v := range s1 {
   fmt.Printf("%c\n", v)
}
```

&emsp;&emsp;正确打印所有字符

### 字符串子串的操作

#### 搜索子串

&emsp;&emsp;使用`strings.Index(s string, substr string)`**正向**搜索子串在原串中的起始下标，找不到返回-1

&emsp;&emsp;使用`strings.LastIndex(s string, substr string)`**反向**搜索子串在原串中的起始下标，找不到返回-1

```go
var s1 string = "hello世界"
pos := strings.Index(s1, "llo")	//2

var s1 string = "hello世界"
pos := strings.LastIndex(s1, "hello")	//0
```

#### 获取子串

&emsp;&emsp;如果要将字符串中的子串拿出单独处理，可以使用切片

```go
var s1 string = "hello世界"
sub := s1[4:8]	//“o世“
```

&emsp;&emsp;下标代表原串中的字节下标，所以要考虑Unicode字符占3字节的情况

### 修改字符串

&emsp;&emsp;Go中的字符串是不可修改的常量，以下操作无法通过编译

```go
var s1 string = "hello世界"
s1[2] = 48
```

&emsp;&emsp;若要修改字符串，只能创建新的字符串，且字节数组是可以修改的

```go
var s1 string = "hello世界"
tmp := []byte(s1)
tmp[2] = 48
s1 = string(tmp)	//"he0lo世界"
```

### 连接字符串

&emsp;&emsp;除了使用`+`拼接字符串，还有其他高效的方法

```go
s1 := "hello "
s2 := "world"
var builder bytes.Buffer
builder.WriteString(s1)
builder.WriteString(s2)
fmt.Println(builder.String())
```

### 格式化

&emsp;&emsp;使用`fmt.Sprintf(format string, a ...interface{})`格式化输出字符串

## 参考资料

《Go语言从入门到实践》第二章

