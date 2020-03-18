# Python学习——正则表达式

[TOC]

&emsp;&emsp;&emsp;&emsp;Python内置了 re 模块提供与 Perl 语言类似的正则表达式操作。模式和被搜索的字符串既可以是Unicode字符串(str)，也可以是8位字节串(bytes)。但是Unicode字符串与8位字节串不能混用：也就是说不能用一个字节串模式去匹配 Unicode 字符串，反之亦然；类似的，执行替换操作时，也不能混用。

### 正则语法

&emsp;&emsp;&emsp;&emsp;[正则表达式](D:\归档\python\正则表达式.md)

### re模块常用函数和方法

#### compile(pattern, flags=0)

&emsp;&emsp;将正则表达式的样式编译为一个**正则表达式对象(class re.Pattern)**(Python版本不同，类也可能不同)，可以用于匹配。

```python
prog = re.compile(pattern)
result = prog.match(string)
```

#### match(pattern, string, flags=0)

&emsp;&emsp;match()函数试图从字符串的**起始**部分对模式进行匹配。匹配 成功返回一个**匹配对象(class re.Match)**，失败则返回 None

```python
res = re.match(r"[0-9]+", "123123")
if res is not None:
    print(res.group())
# 123123
```

&emsp;&emsp;要注意调用group()方法前进行判断，因为 None 没有group()方法，会抛出 AttributeError 异常

```python
res = re.match(r"[0-9]+", "a123123")
print(res.group())
# AttributeError: 'NoneType' object has no attribute 'group'
```

&emsp;&emsp;match()从字符串的**起始**部分开始匹配，在上面的例子中，虽然字符串中有一串可以匹配上，但是从起始部分开始匹配，失败了，所以返回了 None

```python
res = re.match(r"[0-9]+", "123123a")
print(res.group())
# 123123
```

&emsp;&emsp;如果从起始部分开始有子串匹配上，那么就会返回一个**匹配对象**

&emsp;&emsp;match()也可以被**正则表达式对象**调用，按照正则表达式对象进行匹配

```python
pattern = re.compile(r"[0-9]+")
res = pattern.match("123123a")
print(res.group())
# 123123
```

#### fullmatch(pattern, string, flags=0)

&emsp;&emsp;如果整个字符串和正则表达式匹配，返回一个**匹配对象**，否则返回None

```python
res = re.fullmatch(r"\d*","123abc")
print(res)
# None
```

#### search(pattern, string, flags=0)

&emsp;&emsp;match()从字符串起始部分开始匹配，失败就返回 None，显然假如期望在字符串中间找到能够匹配的子串，match()并不适合，此时search()就派上上了用场

&emsp;&emsp;search()的工作方式与match()一致，区别就在于上面提到的，search()会在字符串中任意位置进行匹配，成功匹配返回一个**匹配对象**，失败返回 None

```python
res = re.search(r"[0-9]+", "abc123")
print(res.group())
# 123
```

#### findall(pattern, string[, flags])

&emsp;&emsp;查询字符串匹配正则表达模式的**全部**非重复出现情况，也就是说，比起search()，第一次匹配成功后，findall()会继续向下查找有没有别的匹配的部分，最终返回一个**列表**，如果没有任何匹配的部分，就是一个**空列表**

```python
res = re.findall(r"\d+","12abc123aa321")
print(res)
# ['12', '123', '321']
```

&emsp;&emsp;当匹配含有子组（即用圆括号包裹的部分）的正则表达式时，在返回的匹配成功列表中，包含的是子组组成的元组

```python
res = re.findall(r"(-?\d+)(\.\d+)?","3.14 -4.13")
print(res)
# [('3', '.14'), ('-4', '.13')]
```

#### finditer(*pattern*, *string*, *flags=0*)

&emsp;&emsp;功能与findall相同，但是返回值是一个迭代器类型

#### sub(*pattern*, *repl*, *string*, *count=0*, *flags=0*)

&emsp;&emsp;使用repl替换pattern匹配到的部分，其中，使用\1转义，会替换为匹配到的第二个子组

```python
res = re.sub(r"\d+",r"num","123abc321")
print(res)
# numabcnum
```

```python
res = re.sub(r"(-?\d+)(\.\d+)?", r"整数部分：\1", "3.14")
print(res)
# 整数部分：3
```

&emsp;&emsp;可选参数count不为0时指定最多替换次数

#### subn(pattern, repl, string, count=0, flags=0)

&emsp;&emsp;功能与sub函数相同，不过返回值是包含替换次数的元组 (字符串, 替换次数)

#### split(pattern, string, maxsplit=0, flags=0)

&emsp;&emsp;用pattern匹配到的部分分割字符串，maxsplit是最大分割次数，如果pattern内带有括号，那么该子组也会在分割结果中

```python
# 不带括号
res = re.split(r"-?\d+", "abc123bca-567")
print(res)
# ['abc', 'bca', '']
# 带括号
res = re.split(r"-?(\d+)", "abc123bca-567")
print(res)
# ['abc', '123', 'bca', '567', '']
```

### flag标志

#### re.A & re.ASCII

&emsp;&emsp;让 `\w`, `\W`, `\b`, `\B`, `\d`, `\D`, `\s` 和 `\S` 只匹配ASCII，而不是Unicode。

#### re.I & re.IGNORECASE

&emsp;&emsp;进行忽略大小写匹配

#### re.M & re.MULTILINE

&emsp;&emsp;设置后，`^` 和 `$` 匹配被换行符隔开的每一行的首尾。默认只匹配整个字符串的首尾

#### re.S & re.DOTALL

&emsp;&emsp;设置后，`.` 匹配任何字符，包括换行符。默认不匹配换行符

#### re.X & re.VERBOSE

&emsp;&emsp;允许编写可读性更好的正则语句，包括换行，并在每一行添加注释