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

​		利用该机制实践了[生产者消费者模型](D://学习笔记/) 

### ReentrantLock 和 Condition

​		`Synchronized` 锁重量大，线程获取不到锁会一直等待，有死锁风险

​		`ReentrantLock` 用来替代`Synchronized` 锁，而`Condition` 提供了使用`ReentrantLock`锁时的通知等待机制

​		`ReentrantLock`需要手动释放锁

### `ReentrantReadWriteLock` 

​		`ReadWriteLock`锁是为了读多写少场景下的并发度

​		加锁是为了防止某一资源同时被多个线程读写，因为代码推进顺序的不同导致结果与预期不符。但实际上，只有写操作会导致结果异常，如果多个线程只是想读数据，那么并不会相互影响

​		`ReadWriteLock` 锁就是为了提高读的并发度而设计的。如果一块临界区代码加了读锁，那么就允许多个线程同时持有该锁执行临界区代码，此时申请写锁的线程等待，直到所有读锁被释放；如果一块临界区代码加了写锁，那么只允许一个线程持有锁执行临界区代码，申请读锁的线程等待，直到写锁被释放。**即读锁允许多个线程持有，写锁只允许一个线程持有；读写锁不能同时被不同的线程获取。**（同一线程可能同时持有，因为写锁中可重入读锁，反之不行）

​		该锁是一个悲观的锁，因为**悲观地**认为要读的数据一定会被写操作修改，所以写的时候不允许读

### `StampedLock` 

​		`StampedLock` 锁对`ReentrantReadWriteLock` 进行了改进，允许读写操作可以同时进行，不再让线程等待锁的释放，进一步提高并发度

​		但是这样显然会导致错误的发生，所以在读数据前后，检查期间是否有线程获取写锁修改了数据，如果有就加上一个悲观读锁后重新读数据

​		该锁是一个乐观的锁，因为**乐观的**认为读取数据时没有写操作更改数据，所以读数据时，并不阻塞其他线程写数据的操作