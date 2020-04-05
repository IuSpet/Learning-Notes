# Java学习笔记——List

[TOC]

List：有序集合（也成为序列）。此接口的用户可以精确地控制每个元素在列表中的插入位置。用户可以通过其整数索引访问元素，并搜索列表中的元素。List中有重复的元素和 null

常用的该接口的实现类有：

- ArrayList：内部用数组存储元素
- LinkedList：内部用链表存储元素

该接口提供的常用方法：

- int size() 
- E get(int) 
- E set(int, E) 
- boolean add(E)
- void add(int, E)
- boolean remove(Object)
- E remove(int)

## ArrayList

### 构造方法

#### 无参构造方法

```java
public ArrayList() {
	this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

数组初始化为一个空数组

```java
ArrayList<Object> arrayList = new ArrayList<>();
```

#### 传入int的构造方法

```java
public ArrayList(int initialCapacity) {
	if (initialCapacity > 0) {
    	this.elementData = new Object[initialCapacity];
	} else if (initialCapacity == 0) {
   	 this.elementData = EMPTY_ELEMENTDATA;
	} else {
    	throw new IllegalArgumentException("Illegal Capacity: "+ initialCapacity);
	}
}
```

`initialCapacity` 大于等于0时，初始化为对应大小的数组，小于0则抛出异常

#### 传入Collection的构造函数

```java
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        // defend against c.toArray (incorrectly) not returning Object[]
        // (see e.g. https://bugs.openjdk.java.net/browse/JDK-6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // replace with empty array.
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

传入 Collection 对象后将内部元素复制进数组完成初始化

```java
List<Integer> list = List.of(1,2,3,4,5);
ArrayList<Object> arrayList = new ArrayList<>(list);
```

