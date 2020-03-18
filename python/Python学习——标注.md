# Python学习——标注

### 提示

​		使用标注，作为函数参数和返回值的提示

```python
def func(s: str, num: int) -> str:
	return s
```

​		标注在这里只是提示，使用IDE如PyCharm时，这样定义函数，在调用时，会显示的提示各个参数的要求，方便检查和使用，在定义的函数体内，使用参数也会提示该类型下的方法

​		除去这种办法，使用默认参数也可以达到同样的效果，

```python
def func(s='', num=0):
	return s
```

### 别名