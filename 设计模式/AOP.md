# AOP

### 概念解释

&emsp;&emsp;**AOP (Aspect Orentied Programming , 面向方面编程)**

&emsp;&emsp;面向方面的编程需要把程序逻辑分解成不同的部分称为所谓的关注点。跨一个应用程序的多个点的功能被称为**横切关注点**，这些横切关注点在概念上独立于应用程序的业务逻辑

&emsp;&emsp;程序在运行时，动态的将代码切入到类的指定方法或者说指定位置上

&emsp;&emsp;举例来说，我有个程序，里面有三个应用，访问前都需要登陆，借助于AOP，我可以动态的将登陆模块加到访问方法执行前。像登陆这种的可重用的模块化单元叫做**切面(aspect)**

&emsp;&emsp;有各种各样的常见的很好的方面的例子，如日志记录、审计、声明式事务、安全性和缓存等

#### 名词解释

- ##### 连接点(Join Point)

&emsp;&emsp;程序运行中的一个点，是切点的候选点，是能够插入切面的点

- ##### 通知(Advice)

&emsp;&emsp;指一个连接点在特定的切面要做的事情

- ##### 切点(Pointcut)

&emsp;&emsp;切点就是从连接点中选出的，用来插入切面的点，在代码中，定义 `pointcut` 的下一步就是定义 `aspect` 并且插入合适的位置

  ```xml
  <aop:config>
      <aop:pointcut id="cutAspetc" expression="execution(* example.Play.play())"/>
      <aop:aspect id="cutAspetc" ref="cut">
          <aop:before method="before" pointcut-ref="cutAspetc"/>
          <aop:after method="after" pointcut-ref="cutAspetc"/>
      </aop:aspect>
  </aop:config>
  ```

这是stackoverflow里的高票解释

> **Joinpoint:** A joinpoint is a *candidate* point in the **Program Execution** of the application where an aspect can be plugged in. This point could be a method being called, an exception being thrown, or even a field being modified. These are the points where your aspect’s code can be inserted into the normal flow of your application to add new behavior.
>
> **Advice:** This is an object which includes API invocations to the system wide concerns representing the action to perform at a joinpoint specified by a point.
>
> **Pointcut:** A pointcut defines at what joinpoints, the associated Advice should be applied. Advice can be applied at any joinpoint supported by the AOP framework. Of course, you don’t want to apply all of your aspects at all of the possible joinpoints. Pointcuts allow you to specify where you want your advice to be applied. Often you specify these pointcuts using explicit class and method names or through regular expressions that define matching class and method name patterns. Some AOP frameworks allow you to create dynamic pointcuts that determine whether to apply advice based on runtime decisions, such as the value of method parameters.

- ##### 目标对象(Target Object)

&emsp;&emsp;被一个或者多个切面通知的对象，也就是需要被 AOP 进行拦截对方法进行增强（使用通知）的对象，也称为被通知的对象。

- ##### 切面(Aspect)

&emsp;&emsp;它是一个跨越多个类的模块化的关注点，它是通知和切点合起来的抽象，它定义了一个切点用来匹配连接点，也就是需要对需要拦截的那些方法进行定义；它定义了一系列的通知用来对拦截到的方法进行增强

- ##### 织入(Weaving)

&emsp;&emsp;是将切面应用到目标对象的过程

### Spring AOP编程

#### 准备工作

​		spring的几个jar包自然是必不可少

​		我用idea直接创建的spring项目，但是仍然缺了几个包，导致试了几种方法都一直报错，分别是

- aspectjrt.jar
- aspectjweaver.jar
- aspectj.jar

#### 基于注解的AOP实现