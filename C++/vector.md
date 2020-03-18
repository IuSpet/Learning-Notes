# vector

### 1.constructor

```c++
vector<int> vi1;			//默认，创建一个空的vector对象
vector<int> vi2(20);		//参数指分配空间的大小
vector<int> vi3(20, 10);	//分配的空间赋初值
vector<int> vi4(vi3);		//复制
vector<int> vi5{ 1,2,3,4,5 };
```

### 2.front

​		返回向量第一个

​		向量不能为空

```c++
front() = [0]
```

### 3.back

​		返回向量最后一个

​		向量不能为空

```c++
back() = [size()-1]
```

### 4.begin - end

​		begin()指向开头元素，end()指向结尾元素的后一个

```c++
for(auto p = t.begin(); p != t.end(); p++)
```

### 5.rbegin - rend

​		rbegin()指向结尾元素，rend()指向开头元素的前一个

```c++
for(auto p = t.rbegin(); p != t.rend(); p++)
```

### 6.empty

​		若向量是空的，返回true，否则返回false

```c++
if(t.empty())
```

### 7.size

​		返回向量内的元素数量

### 8.capacity

​		返回为该向量储存元素的空间的大小

### 8.clear

​		清空向量所有的元素

### 9.shrink_to_fit

​		缩小向量，使分配的内存刚好适合元素数量

### 10.push_back

​		往向量的尾部添加元素

```
t.push_back(123);
```

### 11.pop_back

​		删除向量尾部的元素

### 12.erase

​		删除指定位置的元素，返回下一个元素的迭代器

```c++
vector<int>::iterator it = NULL;
it = t.erase(it);
```



