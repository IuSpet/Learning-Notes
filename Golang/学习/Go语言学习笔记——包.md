# Go语言学习笔记——包

[TOC]

&emsp;&emsp;Go语言的源码复用建立在包基础之上

&emsp;&emsp;Go程序入口函数main()必须在main包中

### 包访问权限

&emsp;&emsp;Go中没有public、private之类的关键字

&emsp;&emsp;在包内定义的类型、函数、接口等，如果首字母大写，就是公共的，在其他包中可以访问；反正就只能在当前包中访问

### 导入包

```go
import "fmt"
import "reflect"
```

或者

```go
import (
   "fmt"
   "reflect"
)
```

&emsp;&emsp;**导入包实际导入的是路径，告诉编译器去这些目录下搜索包。但是习惯于将该路径下的包名命名与目录名称相同**

#### 使用自定义包名

&emsp;&emsp;import的路径前加上新的名称

```go
import (
   io "fmt"
   rf "reflect"
)

func main() {
   io.Println("hello")
   var a interface{}
   io.Println(rf.TypeOf(a))
}
```

#### 导入匿名包

&emsp;&emsp;利用自定义包名，将import的目录标示为`_`，即可匿名导入包

&emsp;&emsp;匿名导入的包，会执行包中的init()方法，但不能使用包内的其他方法、类型等

&emsp;&emsp;新建一个foo目录，新建go文件并标示为foo包

```go
package foo

import "fmt"

func init() {
   fmt.Println("from pkg foo")
}
```

&emsp;&emsp;匿名导入该目录

```go
package main

import (
   _ "awesomeProject/foo"
   io "fmt"
)

func main() {
   io.Println("hello")
}
```

&emsp;&emsp;打印输出

```
from pkg foo
hello
```

&emsp;&emsp;foo包中的init()方法执行了

### 包内的init()方法

&emsp;&emsp;当包被导入时，init()方法就会被执行，通过该方法，可以做一些初始化的工作

&emsp;&emsp;init()的特性

- 每个go文件中都可以有一个init()方法，所以一个包中可以有多个init()方法
- init()会在main()执行前调用
- 调用init()的顺序为深度优先，即main->A->B，init()的调用顺序为先B后A
- 同一个包内的init()调用顺序不可预测