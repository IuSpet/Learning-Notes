# java学习笔记——多线程同步

[TOC]

​		线程和进程的一大区别就在于内存的使用上

​		进程是操作系统分配资源的基本单位，各进程使用各自的内存，**互不相干**，进程间通信也只能借助操作系统提供的方法。

​		线程是在同一进程中处理不同子任务的可以调度的单元，所有线程共享同一内存，虽然数据通信变得简单，但是一不注意就会**互相影响**，出现错误

​		所以使用多线程编程，线程间的同步是非常重要的一个方面

​		一般开始学习多线程编程，对于线程调度执行顺序不当导致的问题都很清楚了，我就不举栗子了，直接写解决方案

### synchronized锁

​		对于一个锁，在运行期间，同一时间只能有一个线程持有。利用这个性质，可以要求两个线程可能互相影响的两块代码（临界区），在获得锁的时候才能执行，这样同一时间，只会有一个代码块在运行。

​		加锁的方式也很简单

```java
Object lock = new Object();
synchronized (lock){
      //临界区          
}
```

​		使用锁来确保两个线程的临界区代码执行互不干扰

```java
package example;

public class Work {
    private static Object lock = new Object();
    public static int count;

    static class Work1 implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 100; i++) {
                synchronized (lock) {
                    count *= 2;
                    count += 1;
                }
            }

        }
    }
    static class Work2 implements Runnable{
        @Override
        public void run() {
            for(int i = 0; i < 100; i++)
            {
                synchronized (lock){
                    count -= 1;
                    count /= 2;
                }
            }
        }
    }
}
```

```java
package example;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread thread1 = new Thread(new Work.Work1());
        Thread thread2 = new Thread(new Work.Work2());
        Work.count = 30;
        thread1.start();
        thread2.start();
        System.out.println(Work.count);
        //30
    }
}
```

#### 线程安全类

​		一个类在多线程环境下，多个线程使用互不影响，那么这就是一个线程安全的类

​		要定义一个线程安全的类，就需要对里面的临界代码加锁，因为不同实例对象要用不同的锁，所以锁是 `this`

​		如果想将整个方法锁住，那么可以用 `synchronized` 关键字修饰方法

```java
public synchronized void func(){
	//方法代码
}
//等价于
public void func(){
	synchronized(this){
		//方法代码
	}
}
```

​		静态方法也可以这样修饰，等价于锁住了 `Class` 类的实例

#### 可重入锁

​		一个锁虽然只能被一个线程持有，也就是使用，但是在线程内，可以被多个方法使用

```java
public synchronized void A(){
	B();
}
public synchronized void B(){;}
```

#### 死锁

​		在使用锁的同时，也要注意申请和释放锁的顺序。

​		如果一个线程持有锁A，申请锁B，而另一个线程持有锁B，申请锁A。那么两个线程都无法继续执行下去，锁也不会再释放，没有外力干预，如结束某一线程，或是结束整个进程，那么这个局面会一直持续下去，这就是死锁。

​		如果保持恰当的顺序申请和释放锁，就可避免此类死锁

### 等待通知机制

​		利用 Object 类的 `wait()` 方法使一个线程处于等待状态，再利用 `notify()` 和 `notifyAll()` 方法唤醒等待状态的线程

​		在多线程方法中，调用 `wait()` 的是锁对象，也就是说 `wait()` 必须在 `synchronized` 块中使用。在调用 `wait()` 方法后，锁会释放，而当前线程自然处于等待状态，直到其他线程，该锁调用了 `notify()` 或 `notifyAll()`  方法后，该线程被唤醒，并在获得重新获得锁后返回继续执行