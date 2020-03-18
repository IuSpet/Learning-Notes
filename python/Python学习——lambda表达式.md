# Python学习——lambda表达式

​		lambda表达式，用来创建匿名函数

```python
lambda <参数> : <返回值>

lambda expression1 : expression2
# 等价于
def func(expression1):
	return expression2
```

​		还可以用来创建嵌套函数

```python
def func(x):
	return lambda a:a+x

f = func(5)
# 现在f是一个lambda对象
print(f(3))
# 8
```

​		再多几层也可以

```python
def fun(a):
    return lambda b: lambda c: 100*a+10*b+c

f1 = fun(1)
f2 = f1(2)
print(f2(3))
# 123
```

#### 应用

​		lambda的特性，使得它可以配合Python内建的 `map`，`reduce`，`filter` 函数，用少量代码完成复杂的功能，也体现了Python的哲学

​		map()函数将第二个参数提供可迭代数据分别传给第一个参数的函数处理

```python
# 计算一个列表所有数字的平方
L = [1, 2, 3, 4, 5]
print(list(map(lambda x: x * x, L)))
# [1, 4, 9, 16, 25]
```

​		reduce()函数接收参数类型和map函数相同，但是函数要接收两个参数，嵌套执行整个可迭代数据

```python
redece(fun,[1,2,3,4]) = fun(fun(fun(1,2),3),4)
```

```python
# 计算整个列表的积
L = [1, 2, 3, 4, 5]
print(reduce(lambda a, b: a * b, L))
# 15
```

​		filter()函数用于筛选，第一个参数的函数执行第二个参数内的值，为真留下

```python
# 筛选整个列表内3的倍数
L = [1, 2, 3, 4, 5, 6, 7, 8, 9]
print(list(filter(lambda x: not x % 3, L)))
# [3, 6, 9]
```

