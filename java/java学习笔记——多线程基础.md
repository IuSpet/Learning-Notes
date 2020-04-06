# java学习笔记——多线程基础

### 创建新线程

#### 继承Thread类重写run方法

```java
package example;

public class Work extends Thread {
    @Override
    public void run() {
        super.run();
        System.out.println("new thread start");
    }

}
```

&emsp;&emsp;也可以直接写在创建处

```java
package example;

public class Main {
    public static void main(String[] args) {
        Thread thread = new Thread(){
            @Override
            public void run() {
                super.run();
                System.out.println("new thread start");
            }
        };
        thread.start();
    }
}
```

&emsp;&emsp;用Lambda表达式可以简写这一过程

```java
package example;

public class Main {
    public static void main(String[] args) {
        Thread thread = new Thread(()->{
            System.out.println("new thread start");
        });
        thread.start();
    }
}
```

#### 实现 Runnable 接口，重写 run 方法，作为参数传入Thread()

```java
package example;

public class Work implements Runnable {
    @Override
    public void run() {
        System.out.println("new thread start");
    }
}
```

&emsp;&emsp;或者直接在参数里创建

```java
package example;

public class Main {
    public static void main(String[] args) {
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("new thread start");
            }
        });
        thread.start();
    }
}
```

### Thread 构造方法参数

```java
private Thread(ThreadGroup g, Runnable target, String name,
                   long stackSize, AccessControlContext acc,
                   boolean inheritThreadLocals)
```

- ThreadGroup g：线程组
- Runnable target：要执行的任务
- String name：线程名

### Thread 常用方法

#### currentThread()

&emsp;&emsp;静态方法，返回对当前正在执行的线程对象的引用

#### start()

&emsp;&emsp;在原线程执行，启动新线程，接着会在新线程内自动执行 run() 方法，**一个线程只能执行一次 `start()` 方法**

#### yield()

&emsp;&emsp;指当前线程愿意让出对当前处理器的占用

#### sleep()

&emsp;&emsp;静态方法，使当前线程挂起一段时间

#### join()

&emsp;&emsp;使当前线程等待另一个线程执行完毕之后再继续执行

#### setPriority(int n)

&emsp;&emsp;静态方法，设置线程的优先级，供操作系统调度使用

### 线程的状态

- New：新创建的线程，尚未执行；
- Runnable：运行中的线程，正在执行`run()`方法的Java代码；
- Blocked：运行中的线程，因为某些操作被阻塞而挂起；
- Waiting：运行中的线程，因为某些操作在等待中；
  - Object.wait()：使当前线程处于等待状态直到另一个线程唤醒它；
  - Thread.join()：等待线程执行完毕，底层调用的是Object实例的wait方法；
  - LockSupport.park()：除非获得调用许可，否则禁用当前线程进行线程调度。
- Timed Waiting：运行中的线程，因为执行`sleep()`方法正在计时等待；
  - Thread.sleep(long millis)：使当前线程睡眠指定时间；
  - Object.wait(long timeout)：线程休眠指定时间，等待期间可以通过`notify()/notifyAll()`唤醒；
  - Thread.join(long millis)：等待当前线程最多执行`millis`毫秒，如果`millis`为0，则会一直执行；
  - LockSupport.parkNanos(long nanos)： 除非获得调用许可，否则禁用当前线程进行线程调度指定时间；
  - LockSupport.parkUntil(long deadline)：同上，也是禁止线程进行调度指定时间；
- Terminated：线程已终止，因为`run()`方法执行完毕。

&emsp;&emsp;可以使用 `getState()` 方法查看线程状态，这个方式只是给用户提供一个查看线程状态的方法，并**不能用来进行线程同步控制**

```java
package example;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("new thread start");
                try {
                    Thread.sleep(5000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        System.out.println(thread.getState());
        //NEW
        thread.start();
        System.out.println(thread.getState());
        //RUNNABLE
        Thread.sleep(1000);
        System.out.println(thread.getState());
        //TIMED_WAITING
        thread.join();
        System.out.println(thread.getState());
        //TERMINATED
    }
}
```

### 守护线程

&emsp;&emsp;对于一般线程，如果线程没有结束，那么JVM就不会结束

&emsp;&emsp;而对于守护线程，当所有非守护线程结束，就算**守护线程没结束，JVM也会结束**

&emsp;&emsp;守护线程需要在线程启动前，由线程的引用调用 `setDaemon(true)` 来设置

```java
package example;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(()->{
            try {
                Thread.sleep(100000);
                System.out.println("after sleep");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        thread.setDaemon(true);
        thread.start();
    }
}
```

&emsp;&emsp;运行程序会发现程序立即结束，没有打印任何信息，显然程序并没有等待守护线程结束

### 参考资料

[多线程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1255943750561472)

[深入浅出Java多线程](http://concurrent.redspider.group/)