# java学习笔记——Map

Map：map是一种表示是由键映射到值的对象。map中不能包含重复的键，每个键最多可以映射一个值。

常用实现类：

HashMap

Map接口提供的常用方法：

- int size()

- boolean isEmpty()
- V get(Object)
- V put(K, V)
- K getKey()
- V getValue()
- V remove(Object)
- Set\<k\> keySet()
- Collection\<V\> values()

## HashMap

### 构造方法

#### 无参构造方法

```java
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}
```

使用默认参数初始化，桶大小为16，扩容因子为0.75

```java
HashMap<String,Integer> hashMap = new HashMap<>();
```

#### 指定桶大小的构造方法

```java
public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}
```

使用指定桶大小和默认扩容因子初始化，桶大小为 `initialCapacity` ，扩容因子为0.75

```java
HashMap<String,Integer> hashMap = new HashMap<>(32);
```

#### 指定桶大小和扩容因子的构造方法

```java
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " +
                initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +
                loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
}
```

只传入桶大小的构造函数也是由这个构造函数来执行，从代码细节来看

- 桶大小小于0抛出异常
- 扩容因子小于0或者不是数字抛出异常
- 会根据桶大小的值计算一个2的整数幂次值，具体算法是

```java
static final int tableSizeFor(int cap) {
    int n = -1 >>> Integer.numberOfLeadingZeros(cap - 1);
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

- 桶的大小不会超过 `MAXiMUM_CAPACITY` 

```java
HashMap<String,Integer> hashMap = new HashMap<>(32, 0.8F);
```

#### 传入Map对象的构造方法

```java
public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
}
```

扩容因子初始化为默认值0.75，之后调用 `putMapEntries` 方法插入参数中的元素

```java
HashMap<String,Integer> hashMap = new HashMap<>(Map.of("a",1,"b",2,"c",3));
```

可以发现，四个构造函数，其实都是在做初始化桶大小和扩容因子的工作。直到执行 `putVal` 方法时才会构建内部的数据结构

### 遍历方法

#### 遍历 `entry` 的方式

```java
for (Map.Entry<String,Integer> entry : hashMap.entrySet()) {
    System.out.println(entry.toString());
    //entry.getKey();entry.getValue();
}
```

#### 只遍历 `Key` 或 `Value` 

```java
for(String key:hashMap.keySet()){
    System.out.println(key);
}
for(Integer value: hashMap.values()){
    System.out.println(value);
}
```

#### 利用Lambda表达式遍历

```java
hashMap.forEach((k,v)-> System.out.println(k+"="+v));
```

### 参考资料

[Java中遍历HashMap的5种方式](https://blog.csdn.net/w605283073/article/details/80708943)