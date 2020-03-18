# string

### 1.constructor

```c++
string str1;
string str2("abc");
char a[] = "cba";
string str3(a);
string str4(str3);
string str5(10, 'a');
```

### 2.stoi  stoll

```c++
int b = stoi(str1, 0,10);
```

### 3.push_back

```
str1.push_back('2');
```

### 4.pop_back

```
str1.pop_back();
```

### 5.length size

```c++
str1.length()
str1.size()
```

### 6.end begin rbegin rend

### 7.insert

```
p = str1.insert((p + 2), '#');
```

### 8.erase

```
p = str1.erase((p + 2));
```

### 9.substr

```
auto p = str1.substr(3, 2);
```

### 10.find

```
auto p = str1.find("abc1111d");
```

### 11.front

### 12.back

### 13.append

```
str.append("str");
```

### 14.clear

```
str.clear();
```

