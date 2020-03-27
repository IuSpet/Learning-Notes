# Java学习笔记——面向对象基础

[TOC]

### 类

​		将一个事务抽象构造出来的原型，通过该原型可以创建属于该事务的实例，有了实例可以按照定义好的方法执行操作

### 方法

​		类的实例对象可以执行的操作

### 构造方法

​		如果没有指定构造方法，编译器会自动添加一个无参数、不执行任何操作的默认构造方法。但是如果自己定义了任意一种构造方法，那么编译器就**不会**再添加默认的构造方法。所以，如果自己定义了一个需要参数的构造方法，那么创建该类的实例时，必须要带参数调用该构造方法创建，不能再用无参的默认构造方法。

​		如果在类的定义中给一个属性有默认值，在构造函数中如果修改该属性，那么创建实例时，构造函数中的修改操作会覆盖默认值。

```java
public class Main {
    public static void main(String[] args) {
        cls c = new cls(123);
        //c.tmp = 123
    }
}

class cls {
    private int tmp = 5;
    public cls(int x){
        tmp = x;
    }
}
```

​		在类内可以通过 `this` 调用自身的构造函数

​		在构造函数中可以初始化被 `final` 修饰的属性，也是唯一的修改机会

### 继承

​		通过 `extends` 关键字在创建一个新类时，继承一个类的所有属性和方法，**构造方法不会继承**

​		在子类的构造函数中，用 `super()` 调用父类的构造函数（必须要有，否则编译器也会默认地调用 `super()` 如果父类没有默认构造函数就会报错）

​		使用 `protected` 关键字，使方法和属性可以被子类调用，而外界无法调用

​		可以将子类实例赋值给父类的变量，或者调用父类的构造函数创建父类对象

​		反之，如果本身是一个类贼对象，转型可以成功；否则，产生 `ClassCastException` 异常

```java
public class Main {
    public static void main(String[] args) {
        A a1 = new B(2,3);		//可以
        A a2 = new A(3,4);
        B b1 = a1;				//可以，a1本身就是B类的实例
        B b2 = a2;				//不可以
    }
}
class A {
    protected int a;
    A(int x) {
        a = x;
    }
}
class B extends A {
    protected int b;
    B(int x, int y) {
        super(x);
        b = y;
    }
}
```

​		可以使用 `instanceof` 来判断是否可以转型，即是否是该类型的一个实例。 `instanceof` 是一个**运算符**，不是函数

​		使用 `final` 修饰一个类，可以使该类无法被继承

### 重载和重写

​		重载是函数具有相同的名称，但是参数表不同，在调用时编译器会自动选择合适的函数。

​		重写是在子类重写父类或者重写接口内的方法，有相同的返回类型和参数表，在调用时会根据调用对象的不同选择合适的方法。**在重写的方法前写下`@Override`可以让编译器检查该函数是否在重写父类的重名函数。**

```java
public class Main {
    public static void main(String[] args) {
        Person p = new Student();
        p.run(); // “Student.run”
    }
}

class Person {
    public void run() {
        System.out.println("Person.run");
    }
}

class Student extends Person {
    @Override
    public void run() {
        System.out.println("Student.run");
    }
}
```

​		重写使得某一类对象在调用时，编译器选择最符合的方法进行调用（多态性）

​		使用 `final` 修饰的方法不能被子类重写

#### 重写Object方法

​		所有类都是 Object 类的子类，所以在类中可以重写Object类的方法

Object类内定义的常用方法

- `toString()`：把instance输出为`String`；
- `equals()`：判断两个instance是否逻辑相等；
- `hashCode()`：计算一个instance的哈希值

### 多态

​		多态是指，针对某个类型的方法调用，其真正执行的方法取决于运行时期实际类型的方法

​		通过多态，代码种只要调用抽象类或者接口的方法，在运行时，会根据不同的实例类型执行不同的方法

