# Go语言入门经典第六章笔记（数组、切片、映射）

### 数组

声明和初始化数组

```go
var str [10]string
num := []int{1, 2, 3, 4, 5}
var d = []float64{1.1, 2.2, 3.3}
```

### 切片

##### 创建切片

```go
var str = make([]string, 2)
```

##### 增加元素

```go
str = append(str, "hello")
```

`append`会在切片后开辟一个新的空间放入新内容，`append`是一个可变参数函数，可以一次添加多个元素

如果要将另一个切片添加到加到后面，写法为

```go
nums1 := make([]int,5)
nums2 := make([]int,2)
nums2 = append(nums2,nums1...)
```

##### 复制切片

创建一个和要复制的切片类型相同的切片，使用copy函数完成复制

```go
var nums = make([]int, 10)
var nums2 = make([]int,10)
copy(nums2,nums)
```

##### 从切片中删除元素

利用append实现切片内连续元素删除

```go
nums := make([]int,5)
	for i,_ := range nums{
		nums[i] = i
	}
fmt.Println(len(nums))
//5
fmt.Println(nums)
//[0 1 2 3 4]
nums = append(nums[:2],nums[2+2:]...)
fmt.Println(len(nums))
//3
fmt.Println(nums)
//[0 1 4]
```

### 映射

声明

```go
var players = make(map[string]int)
```

其中string为key，int为value

修改，增加元素

```go
players["cook"]=32
```

删除元素

```go
delete(players,"cook")
```

