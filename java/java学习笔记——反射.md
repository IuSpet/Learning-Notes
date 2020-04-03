# java学习笔记——反射

[TOC]

​		Java中类是由JVM在运行中动态加载的。JVM在第一次读取到这个类时，会将它加载进内存，每加载一个类，JVM会创建一个Class类的实例（ `java.lang.Class` ），这个类的实例只有JVM能够创建。

​		在Class类的实例中，会保存加载的类的所有信息，包括类名、方法、包名等。通过这些实例，**就能获取到一个类的所有信息**，这就是反射

### 动态加载机制

​		JVM只会加载用到的类，还未运行到或者没用到的类，并不会加载进内存

### 获取 `Class` 类的实例

#### 通过类的class静态变量获取

```java
Class cls = String.class;
System.out.println(cls.toString());
//class java.lang.String
```

#### 通过类的实例获取

```java
String string = "Hello World";
Class cls = string.getClass();
System.out.println(cls.toString());
//class java.lang.String
```

#### 从完整类名获取

```java
Class cls = Class.forName("java.lang.String");
System.out.println(cls.toString());
//class java.lang.String
```

除了类和接口外，数组和基本数据类型也有对应的Class实例

```java
String[] strings = {"Hello","World"};
Class cls = strings.getClass();
System.out.println(cls.toString());
//class [Ljava.lang.String;
cls = int.class;
System.out.println(cls.toString());
//int
```

### 访问、修改字段内容

#### public Field getField(String name) (Class.java: 1991)(jdk13)

> &emsp;Returns a Field object that reflects the specified public member field of the class or interface represented by this Class object. The name parameter is a String specifying the simple name of the desired field.
> &emsp;The field to be reflected is determined by the algorithm that follows. Let C be the class or interface represented by this object:
> &emsp;If C declares a public field with the name specified, that is the field to be reflected.
> &emsp;If no field was found in step 1 above, this algorithm is applied recursively to each direct superinterface of C. The direct superinterfaces are searched in the order they were declared.
> &emsp;If no field was found in steps 1 and 2 above, and C has a superclass S, then this algorithm is invoked recursively upon S. If C has no superclass, then a NoSuchFieldException is thrown.
> &emsp;If this Class object represents an array type, then this method does not find the length field of the array type.

​		该方法需要一个 `String` 参数，代表一个具体的**公共字段**名。查找顺序为，先在类中查找，没有的话按顺序查找这个类实现的接口，还找不到的话就去它的父类重复这个过程。对于数组字段，不会返回数组大小

```java
package inflect;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException {
        Class cls = C.class;
        try {
            System.out.println(cls.getField("a"));
            //public int inflect.C.a
            System.out.println(cls.getField("b"));
            //public static final int inflect.A.b
            System.out.println(cls.getField("c"));
            //public int inflect.B.c
            System.out.println(cls.getField("array"));
            //public int[] inflect.C.array
        } catch (NoSuchFieldException e) {
            System.out.println(e.toString());
        }
    }
}
interface A{
    int a = 101;
    int b = 101;
}
class B{
    public int a = 202;
    public int b = 202;
    public int c = 202;
}
class C extends B implements A{
    public int a = 303;
    public int[] array = {1,2,3,4,5};
}
```

#### public Field[] getFields()（Class.java: 1810)(jdk13)

> &emsp;Returns an array containing Field objects reflecting all the accessible public fields of the class or interface represented by this Class object.
> &emsp;If this Class object represents a class or interface with no accessible public fields, then this method returns an array of length 0.
> &emsp;If this Class object represents a class, then this method returns the public fields of the class and of all its superclasses and superinterfaces.
> &emsp;If this Class object represents an interface, then this method returns the fields of the interface and of all its superinterfaces.
> &emsp;If this Class object represents an array type, a primitive type, or void, then this method returns an array of length 0.
> &emsp;The elements in the returned array are not sorted and are not in any particular order.

​		返回一个类或接口对象能访问的所有**公共字段**，包括父类和父接口的公共字段；对于没有可访问公共字段的类或接口，还有数组类型、基元类型或void，返回长度为0的数组。返回的结果不带有任何排序

```Java
package inflect;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException {
        Class cls = C.class;
        for(var field:cls.getFields())
            System.out.println(field);
    }
}
interface A{
    int a = 101;
    int b = 101;
}
class B{
    public int a = 202;
    public int b = 202;
    protected int c = 202;
}
class C extends B implements A{
    public int a = 303;
    private int d = 303;
    public int[] array = {1,2,3,4,5};
}
```

```java
//打印结果
public int inflect.C.a
public int[] inflect.C.array
public static final int inflect.A.a
public static final int inflect.A.b
public int inflect.B.a
public int inflect.B.b
```

#### public Field getDeclaredField(String name) (Class.java: 2403)(jdk13)

> &emsp;Returns a Field object that reflects the specified declared field of the class or interface represented by this Class object. The name parameter is a String that specifies the simple name of the desired field.
> If this Class object represents an array type, then this method does not find the length field of the array type.

​		输入一个字段名称，如果在该类中能找到，返回一个 `Field` 对象。这个字段**不再限于公共字段**，但是也不会再去它的接口和父类去查找

```java
package inflect;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        Class cls = C.class;
        System.out.println(cls.getDeclaredField("d"));
        //private int inflect.C.d
    }
}
class C{
    public int a = 303;
    private int d = 303;
    public int[] array = {1,2,3,4,5};
}
```

#### public Field[] getDeclaredFields() (Class.java: 2244)(jdk13)

> Returns an array of Field objects reflecting all the fields declared by the class or interface represented by this Class object. This includes public, protected, default (package) access, and private fields, but excludes inherited fields.
> If this Class object represents a class or interface with no declared fields, then this method returns an array of length 0.
> If this Class object represents an array type, a primitive type, or void, then this method returns an array of length 0.
> The elements in the returned array are not sorted and are not in any particular order.

​		返回一个 `Field` 数组，包含这个类或接口的所有字段，对于没有字段的对象返回长度为0的数组，返回结果不带任何排序

```java
package inflect;

public class Main {
    public static void main(String[] args){
        Class cls = C.class;
        for(var val:cls.getDeclaredFields())
        {
            System.out.println(val);
            //public int inflect.C.a
			//private int inflect.C.d
			//protected int inflect.C.e
			//public int[] inflect.C.array
        }
    }
}
class C{
    public int a = 303;
    private int d = 303;
    protected int e = 303;
    public int[] array = {1,2,3,4,5};
}
```

#### 通过 `Field` 实例访问修改对象对应字段的值

​		通过上述四种方法可以获得 `Field` 对象，就可以用来修改相应的类的实例的对应字段的内容

```java
package inflect;

import java.lang.reflect.Field;
import java.lang.reflect.Modifier;
import java.rmi.MarshalledObject;

public class Main {
    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
        Object object = new C();
        Class cls = object.getClass();
        Field field = cls.getField("a");
        System.out.println("a: "+field.get(object));//a: 303
        field.set(object,321);
        System.out.println("a: "+field.get(object));//a: 321
        //private字段只能通过带Declared的方法获取
        Field privateField = cls.getDeclaredField("d");
        //判断该字段的访问权限
        System.out.println(Modifier.isPrivate(privateField.getModifiers()));//true
        //不调用该方法，修改private对象会抛出IllegalAccessException异常
        privateField.setAccessible(true);
        //修改private字段内容
        privateField.set(object,404);
        System.out.println(privateField.get(object));//404
    }
}
class C{
    public int a = 303;
    private int d = 303;
    protected int e = 303;
    public int[] array = {1,2,3,4,5};
}
```

#### 小结

- 对于一个对象object
- 通过 `object.getclass()` 获得它的类加载实例 cls
- 通过 `cls.getFields()` 等方法获得object的属性字段 field
- 通过 `field.get(object)` 获取 object 对应字段的值
- 通过 `field.set(object,value)` 将 object 对应字段的值设置为 value

### 调用方法

- public Method[] getMethods() (Class.java: 1900)(jdk13)

- public Method getMethod(String name, Class<?>... parameterTypes) (Class.java: 2100)(jdk13)

- public Method[] getDeclaredMethods() (Class.java: 2305)(jdk13)
- public Method getDeclaredMethod(String name, Class<?>... parameterTypes) (Class.java: 2467)(jdk13)

这四个方法与上面四个获取字段的方法类似，参数为方法名和参数表，返回 `Method` 实例

#### 使用 `Method` 实例调用方法

```java
package inflect;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        Object object = new A();
        Class cls = object.getClass();
        //根据方法名和参数表获取方法
        Method method = cls.getDeclaredMethod("fun", int.class, int.class, C.class);
        //调用方法
        int ans = (int) method.invoke(object,2, 3, new C(4));
        System.out.println(ans);
        //24
    }
}
class A {
    public int fun(int a, int b, C c) {
        return a * b * c.getC();
    }
}
class C {
    private int c;
    C(int c) {
        this.c = c;
    }
    int getC() {
        return c;
    }
}
```

#### 小结

- 对于一个对象 object
- 通过 `object.getclass()` 获得它的类加载实例 cls
- 通过 `cls.getMethods()` 等方法获得 object 能够调用的方法 method
- 通过 `method.invoke(object,args)`  调用object的对应方法

### 参考资料

https://www.liaoxuefeng.com/wiki/1252599548343744/1255945147512512

