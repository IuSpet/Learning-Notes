# Go语言学习笔记——http包

### 客户端

直接对目标url发出get、post请求。put等请求不行

```go
resp, err := http.Get(url)
resp, err := http.Post(url)
```

#### 构造req后发出请求

```go
req,err := http.NewRequest(http.MethodPost,url,nil)
```

#### 实际构造出这样一个结构体

```go
req := &Request{
  ctx:     ctx,
  Method:   method,
  URL:     u,
  Proto:    "HTTP/1.1",
  ProtoMajor: 1,
  ProtoMinor: 1,
  Header:   make(Header),  //map[string][]string
  Body:    rc,
  Host:    u.Host,
}
```

#### 构造出的req可以设置header

```go
req.Header.Set(key,value)
//上面的方法封装了额外的检查和纠正
req.Header["Content-Type"] = []string{"json"}
```

#### 用客户端执行req，发出请求

```go
c := &http.Client{}
c.Do(req)
```

### 服务端

#### Handler

处理器，负责处理客户端发来的req

Handler都是实现了Handler接口的类型，可以自己创建，也有内置的常用的可以用

```go
type Handler interface {
  ServeHTTP(ResponseWriter, Request)
}
```

#### ServerMux

ServerMux是一个请求路由器，负责把收到的请求交给对应的Handler

```go
mux := http.NewServeMux()
r := http.RedirectHandler("https://golang.org", 307)
mux.Handle("/foo", r)
http.ListenAndServe(":8080",mux)
```

来自8080端口的请求都交给了mux，mux再将路由为/foo的请求交给r去处理，实现一个重定向的处理流程

#### 将函数作为处理器

除了创建新类型实现Handler接口，还可以将签名满足Handler接口的函数直接转换为处理器使用

```go
func Tmp(w http.ResponseWriter, r *http.Request) {
  fmt.Println("this is a handler")
}
func main() {
  mux := http.NewServeMux()
  r := http.HandlerFunc(Tmp)
  mux.Handle("/foo", r)
  http.ListenAndServe(":8080",mux)
}
```

还可以继续精简，省略创建Handler实例那一步

```go
func Tmp*(*w http.ResponseWriter, r *http.Request*)* *{*
  fmt.Println*(*"this is a handler"*)*
*}*
func main*()* *{*
  mux := http.NewServeMux*()*
  mux.HandleFunc*(*"/foo", Tmp*)*
  http.ListenAndServe*(*":8080",mux*)*
*}*
```

对于需要额外参数的处理器，还可以用闭包巧妙的完成

#### DefaultServeMux

在调用http.ListenAndServe(addr,handler)时，如果handler是nil，http会使用默认的ServeMux

对于默认的ServeMux，对应的方法直接在http包内

```go
func Tmp*(*w http.ResponseWriter, r *http.Request*)* *{*
  fmt.Println*(*"this is a handler"*)*
*}*
func main*()* *{*
  http.HandleFunc*(*"/foo", Tmp*)*
  http.ListenAndServe*(*":8080",nil*)*
*}*
```

