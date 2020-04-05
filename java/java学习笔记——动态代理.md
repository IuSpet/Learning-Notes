# java学习笔记——动态代理

### 理解

&emsp;&emsp;**为什么要用动态代理？动态代理有什么用？**

&emsp;&emsp;考虑这样一个问题，有一个接口，有多个类实现了这个接口的方法，此时想要在这个接口的某一方法执行前后，额外执行一些操作，如何更改代码？

&emsp;&emsp;从静态的代码角度考虑，自然是去修改每个类的方法，加入这些额外的操作。但是问题也显而易见，那就是每次修改都要深入到每个类，非常麻烦。

&emsp;&emsp;动态代理就是为了解决这个问题。从字面上理解，动态：即执行方法的对象是在代码执行过程中动态绑定的；代理：由一个代理对象替我们操作对象执行方法。那么对于这个代理对象，在运行中，它可以拿到具体的对象，也可以控制该对象调用方法执行，那么在调用方法前后，就可以加上我们想要的额外操作。

### 实现

&emsp;&emsp;有一个接口提供玩的方法

```java
public interface IPlay {
    void play(String string);
}
```

&emsp;&emsp;Student类实现这一接口

```java
public class Student implements IPlay {
    @Override
    public void play(String game) {
        System.out.println("play " + game);
    }
}
```

&emsp;&emsp;实现在 play 前后打印开始和结束

#### 使用反射

&emsp;&emsp;显然，简单的代理，只要告知它对象和方法，再传入相应的参数，就能完成这个操作。那么借助反射，可以轻松完成这一过程

```java
public class ProxyInvokePlay {
    public Object invoke(Object object, Method method, Object[] args) {
        try {
            System.out.println("start");
            method.invoke(object, args);
            System.out.println("end");
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

#### 实现 `InvocationHandler` 接口

&emsp;&emsp;前面使用的 `invoke` 方法在 `InvocationHandler` 中已经定义好了，写一个实现类即可

```java
public class ProxyInvokePlay implements InvocationHandler {
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("start");
        method.invoke(proxy,args);
        System.out.println("end");
        return null;
    }
}
```

&emsp;&emsp;将要代理的对象交给代理对象

```java
IPlay object = new Student();
ProxyInvokePlay proxyInvokePlay = new ProxyInvokePlay();
proxyInvokePlay.invoke(object, object.getClass().getMethod("play", String.class), new Object[]{"over watch"});
//start
//play over watch
//end
```

### 利用动态代理创建类

&emsp;&emsp;使用静态方法 `Proxy.newProxyInstance()` 创建接口的实例

```java
InvocationHandler invocationHandler = new ProxyInvokePlay();
IPlay play = (IPlay) Proxy.newProxyInstance(IPlay.class.getClassLoader(), new Class[]{IPlay.class}, invocationHandler);
play.play("over watch");
```

&emsp;&emsp;看似是直接创建了一个接口的实例，实际上打印返回的 play 的信息可以发现

```java
Class cls = play.getClass();
System.out.println(cls.toString());
System.out.println("all fields:");
for (var v : cls.getDeclaredFields()) {
    System.out.println(v);
}
System.out.println("all methods: ");
for (var v : cls.getDeclaredMethods()) {
    System.out.println(v);
}
System.out.println("all constructors: ");
for (var v : cls.getDeclaredConstructors()) {
    System.out.println(v);
}
```

```java
class example.$Proxy0
all fields:
private static java.lang.reflect.Method example.$Proxy0.m1
private static java.lang.reflect.Method example.$Proxy0.m2
private static java.lang.reflect.Method example.$Proxy0.m3
private static java.lang.reflect.Method example.$Proxy0.m0
all methods: 
public final boolean example.$Proxy0.equals(java.lang.Object)
public final java.lang.String example.$Proxy0.toString()
public final int example.$Proxy0.hashCode()
public final void example.$Proxy0.play(java.lang.String)
all constructors: 
public example.$Proxy0(java.lang.reflect.InvocationHandler)
```

&emsp;&emsp;实际上返回的是一个 `$Proxy0` 类的实例，重写了除 `clone()` 外的所有方法，并创建了对应的 `Method` 实例

&emsp;&emsp;而对于接口中各个方法的内容，可以由实现了 `InvocationHandler` 接口的类的实例来写

&emsp;&emsp;我不太理解的是，可以发现在 fields 中没有 `InvocationHandler` 对象，那么在调用方法时如何调用传入的 `InvocationHandler` 对象的 `invoke` 方法？单步调试也是直接跳到 `invoke` 方法内，等java学的再好点再填坑吧

```java
class ProxyInvokePlay implements InvocationHandler {
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
        /*
        	可以利用method的名字来执行不同的操作
        */
        return null;
    }
}
```

### 应用

&emsp;&emsp;使用如Spring等支持AOP的框架进行面向切面编程，灵活的在一个方法执行前、后等连接点切入额外的模块功能，增强一个方法的功能

### 参考资料

[动态代理 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1264804593397984)