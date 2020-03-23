# IoC

### 名词解释

##### 依赖倒置原则(DIP)：一种软件软件架构设计原则

- 上层模块不应该依赖于下层模块，它们共同依赖于一个抽象。 　

- 抽象不能依赖于具象，具象依赖于抽象。

&emsp;&emsp;依赖倒置解决的问题是：如果上层模块依赖下层模块（依赖是指上层模块在类内创建下层模块实例，根据下层模块提供的方法编写上层模块的功能），每当下层模块需要修改，上层模块也要一并修改

##### 控制反转(IoC)：一种反转流、依赖和接口的方式（DIP的具体实现方式）

&emsp;&emsp;将设计好的类交给系统控制，而不是在类的内部控制，称为控制反转

##### 依赖注入(DI)：IoC的一种实现方式，用来反转依赖（IoC的具体实现方式）

&emsp;&emsp;如果不直接在类内实例底层模块，就要提供其他方法传入要执行的底层模块，即依赖注入

##### **IoC容器：**依赖注入的**框架**，用来映射依赖，管理对象创建和生存周期（DI框架）

### 具体实现

在Java Spring框架下，有三种依赖注入方式

- 构造函数注入

- setter注入

- 基于注解的注入

  ​	假设使用场景为用户使用媒体播放器播放媒体文件，下面演示三种IoC实现方式

#### 构造函数注入

```java
package example1;

public class Media {
    public Media() {
        System.out.println("创建媒体文件");
    }
}
```

```java
package example1;

public class MediaPlayer {
    private Media media;
    public MediaPlayer(Media media) {
        this.media = media;
        System.out.println("创建播放器");
    }
    public void play(){
        System.out.println("播放器播放");
    }
}
```

```java
package example1;

public class User {
    MediaPlayer mediaPlayer;
    public User(MediaPlayer mediaPlayer){
        this.mediaPlayer = mediaPlayer;
        System.out.println("产生用户");
    }
    public void play(){
        mediaPlayer.play();
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="user" class="example1.User">
        <constructor-arg ref="mediaPlayer"/>
    </bean>
    <bean id="mediaPlayer" class="example1.MediaPlayer">
        <constructor-arg ref="media"/>
    </bean>
    <bean id="media" class="example1.Media"/>
</beans>
```

```java
package example1;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        User user = (User) context.getBean("user");
        user.play();
    }
}
```

运行结果

```
创建媒体文件
创建播放器
产生用户
播放器播放
```

​		显然，在代码中并没有实例化 `Media` 、`MediaPlayer` 、`User` 类，即这些类并不是由我们直接在类内控制，而是全部交给了系统控制，这就是控制反转。这些类之间的依赖关系，也全部交给了xml文件来管理，而不是在代码内部产生依赖。