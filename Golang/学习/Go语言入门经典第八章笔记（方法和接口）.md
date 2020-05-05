# Go语言入门经典第八章笔记（方法和接口）

### 使用方法

```go
type Movie struct {
   Name string
   Rating float64
}

func (m *Movie) summary() string{
   //code
}
//也可以传值引用
func (m Movie) summary() string{
   //code
}
```

定义方法比起定义函数，在func后面，要添加一个额外参数，代表接收者，即调用该方法的对象，也等于指出了运行调用该方法的类型

### 使用接口

#### 声明接口

```go
type Shape interface {
   getArea() float64
}
```

​		在接口中只给出方法声明

#### 使用接口

定义接口内声明的所有方法，就算实现了该接口

```go
type Rectangle struct {
   Length float64
   width  float64
}

func (r Rectangle) getArea() float64 {
   return r.Length * r.width
}
```

实现了`Shape`接口内声明的方法后，结构体`Rectangle`就算一个实现了该接口的类型

```go
type Circle struct {
   radius float64
}

func (c Circle) getArea() float64 {
   return math.Pi * c.radius * c.radius
}
```

同理此时`Cirecle`类型也实现了该接口

使用时，就可以声明`Shape`接口的对象，然后在运行中再确定实际的类型

```go
var shape Shape
shape = Rectangle{3, 4}
shape.getArea()

shape = Circle{3}
shape.getArea()
```