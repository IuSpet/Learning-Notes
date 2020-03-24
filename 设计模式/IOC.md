# IoC

[TOC]

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

在Java平台的Spring框架下，有三种依赖注入方式

- 构造函数注入

- setter注入

- 基于注解的注入

  ​	假设使用场景为用户使用媒体播放器播放媒体文件，下面演示三种IoC实现方式

#### 构造函数注入

##### Media接口和实现类

```java
package example1;

public interface MediaInterface {
}
```

```java
package example1;

public class Media implements MediaInterface{
    public Media() {
        System.out.println("创建媒体文件");
    }
}
```

##### MediaPlayer接口和实现类

```java
package example1;

public interface MediaPlayerInterface {
    void play();
}
```

```java
package example1;

public class MediaPlayer implements MediaPlayerInterface {
    private MediaInterface media;

    public MediaPlayer(MediaInterface media) {
        this.media = media;
        System.out.println("创建播放器");
    }

    @Override
    public void play() {
        System.out.println("播放器播放");
    }
}
```

##### User接口和实现类

```java
package example1;

public interface UserInterface {
    void play();
}

```

```java
package example1;

public class User implements UserInterface {
    MediaPlayerInterface mediaPlayer;

    public User(MediaPlayerInterface mediaPlayer) {
        this.mediaPlayer = mediaPlayer;
        System.out.println("产生用户");
    }

    @Override
    public void play() {
        mediaPlayer.play();
    }
}

```

##### Beans配置文件

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

##### 主函数入口

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

#### setter注入

&emsp;&emsp;所有接口文件不变，修改实现类和Beans配置文件

##### Media类

```java
package example1;

public class Media implements MediaInterface{
    public Media() {
        System.out.println("创建媒体文件");
    }
}
```

##### MediaPlayer类

```java
package example1;

public class MediaPlayer implements MediaPlayerInterface {
    private MediaInterface media;

    public void setMedia(MediaInterface media) {
        this.media = media;
        System.out.println("创建播放器");
    }

    @Override
    public void play() {
        System.out.println("播放器播放");
    }
}

```

##### User类

```java
package example1;

public class User implements UserInterface {
    MediaPlayerInterface mediaPlayer;

    public void setMediaPlayer(MediaPlayerInterface mediaPlayer) {
        this.mediaPlayer = mediaPlayer;
        System.out.println("产生用户");
    }

    @Override
    public void play() {
        mediaPlayer.play();
    }
}

```

##### Beans配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="example1.User">
        <property name="mediaPlayer" ref="mediaPlayer"/>
    </bean>

    <bean id="mediaPlayer" class="example1.MediaPlayer">
        <property name="media" ref="media"/>
    </bean>

    <bean id="media" class="example1.Media"/>

</beans>
```

#### 基于注解的注入

&emsp;&emsp;Spring提供了多种注解应对各种情况，这里只用到最简单的 `@Component` 和 `@Autowired` 

##### @Component

作用：将当前类对象存入Spring容器中，默认value是类名首字母小写

使用范围：类前

##### @Autowired

作用：按照类型从Spring容器中选择匹配数据类型注入

使用范围：属性前，方法前

##### Media类

```java
package example1;

import org.springframework.stereotype.Component;

@Component
public class Media implements MediaInterface {
    public Media() {
        System.out.println("创建媒体文件");
    }
}
```

##### MediaPlayer类

```java
package example1;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class MediaPlayer implements MediaPlayerInterface {

    @Autowired
    private MediaInterface media;

    @Override
    public void play() {
        System.out.println("播放器播放");
    }
}
```

##### User类

```java
package example1;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class User implements UserInterface {
    @Autowired
    MediaPlayerInterface mediaPlayer;

    @Override
    public void play() {
        mediaPlayer.play();
    }
}
```

##### Beans配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="example1"/>

</beans>
```

&emsp;&emsp;显然，在这些代码中并没有实例化 `Media` 、`MediaPlayer` 、`User` 类，即这些类并不是由我们直接在类内控制，而是全部交给了系统控制，这就是控制反转。这些类之间的依赖关系，也全部交给了xml文件来管理，而不是在代码内部产生依赖。

&emsp;&emsp;假如要写一个新的下层类，那么只要下层类实现上层类需要的操作，然后放入Spring容器中，Spring就会自动完成组装，而不需要我们在类内更改依赖关系