# Go语言入门经典第七章笔记（结构体）

### 结构体

#### 声明结构体

```go
type Movie struct {
   Name   string
   Rating float64
}
```

#### 创建结构体对象

```go
var a Movie
b := new(Movie)
m := Movie{
   Name:   "Citizen Kane",
   Rating: 10,
}
```

#### 访问结构体内容

```go
fmt.Println(m.Name)
fmt.Println(m.Rating)
```

