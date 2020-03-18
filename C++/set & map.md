# set & map

## set

​		集合、值不能重复，内部排序（默认或者规定），红黑树实现

### 1.constructor

```c++
set<int> si1;
set<int> si2(a, b); //[a,b)
set<int> si3(si2);
set<int> si4{ 1,2,3,4,5 };
set<int, greater<int>> si5;
set<point,cmp> sp1;
```

### 2.insert

​		往集合内插入数据

```c++
pair<iterator,bool> insert(const value_type& x)
iterator insert(iterator position, const value_type& x)
void insert(InputIterator first, InputIterator last)
```

### 3.erase

​		删去集合中的数据

```c++
void erase(iterator position)
size_type erase(const key_type& x)
void erase(iterator first, iterator last)
```

### 4.clean

​		清空集合

### 5.find

​		集合中查找元素，成功返回该值的迭代器，否则返回end()

```c++
 iterator find(const key_type& x)
```

### 6.count

​		返回集合内该元素的个数		

```
size_type count(const key_type& x) const
```

### 7.begin - end

### 8.rbegin - rend

## map

​		集合映射，pair<key, value>键值对，键不能重复，以键排序，红黑树实现

​		每个元素是个pair<>对

### 1.constructor

```c++
map<int, string> mis;
map<int, string> mis2(mis.begin(), mis.end());
map<int, string> mis3(mis2);
map<int, int> mis4{ {1,2},{2,3},{3,4} };
```

### 2.insert

​		插入键值对

```c++
pair<iterator,bool> insert(const value_type& x)
iterator insert(iterator position, const value_type& x)
void insert(InputIterator first, InputIterator last)
```

### 3.erase

​		删除键值对

```
void erase(iterator position)
size_type erase(const key_type& x)
void erase(iterator first, iterator last)
```

### 4.clean

​		清空映射

### 5.find

​		查找键的位置，找不到返回end()

```c++
 iterator find(const key_type& x)
```

### 6.begin - end

### 7.rbegin - rend

### 8.count

```
size_type count(const key_type& x) const
```

