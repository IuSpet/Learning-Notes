# Java多线程——生产者消费者模型

[TOC]

​		多线程同步的经典模型，当初上操作系统时，老师拿这个模型讲解信号量同步机制，可惜C的信号量太难，实验课也是拿助教不知道哪里找的代码跑了跑，一直没有自己实现生产者消费者模型

​		如今正在学习java的多线程，准备用经典的生产者消费者实践一下

### 生产者

```java
package example;

import java.util.Random;

public class Producer extends Thread {
    public Producer(String name) {
        super(name);
    }

    @Override
    public void run() {
        super.run();
        Product product = Product.getInstance();
        Random random = new Random();
        while (Product.produced < Product.target) {
            try {
                product.produce(currentThread());
                //随机产生一个等待时间，使线程切换发挥作用
                int delay = random.nextInt(1000);
                Thread.sleep(delay);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 消费者

```java
package example;

import java.util.Random;

public class Consumer extends Thread {
    public Consumer(String name) {
        super(name);
    }

    @Override
    public void run() {
        super.run();
        Product product = Product.getInstance();
        Random random = new Random();
        while (Product.consumed < Product.target) {
            try {
                product.consume(currentThread());
                //随机产生一个等待时间，使线程切换发挥作用
                int delay = random.nextInt(1000);
                Thread.sleep(delay);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 产品

```java
package example;

public class Product {
    private static final Product INSTANCE = new Product();
    //当前产品数
    private int product_count;
    //产品缓冲区最大容量
    private int max_capacity;
    //已生产和消费的产品数量
    public volatile static int produced = 0;
    public volatile static int consumed = 0;
    //目标
    public final static int target = 30;

    private Product() {
        this.product_count = 0;
        this.max_capacity = 3;
    }

    public static synchronized Product getInstance() {
        return INSTANCE;
    }

    public synchronized void produce(Thread thread) throws InterruptedException {
        //如果产品缓冲区满，则等待
        while (Product.produced < Product.target && product_count >= max_capacity) {
            this.wait();
        }
        if (Product.produced < Product.target) {
            product_count++;
            Product.produced++;
            System.out.println("线程 " + thread.getName() + " 生产了产品" + Product.produced + "，此时产品数量" + product_count);

        }
        //唤醒所有等待线程，消费者线程从等待中恢复
        this.notifyAll();
    }

    public synchronized void consume(Thread thread) throws InterruptedException {
        //如果产品缓冲区空，则等待
        while (Product.consumed < Product.target && product_count <= 0) {
            this.wait();
        }
        if (Product.consumed < Product.target) {
            product_count--;
            Product.consumed++;
            System.out.println("线程 " + thread.getName() + " 消费了产品" + Product.consumed + "，此时产品数量" + product_count);

        }
        //唤醒所有进程，生产者线程从等待中恢复
        this.notifyAll();
    }
}
```

### 测试代码

```java
package example;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread consumer1 = new Consumer("消费者1");
        Thread consumer2 = new Consumer("消费者2");
        Thread producer1 = new Producer("生产者1");
        Thread producer2 = new Producer("生产者2");
        Thread producer3 = new Producer("生产者3");
        //依次启动
        consumer1.start();
        consumer2.start();
        producer1.start();
        producer2.start();
        producer3.start();
        //等待所有消费者线程结束
        consumer1.join();
        consumer2.join();
    }
}
```

### 测试结果

```
线程 生产者2 生产了产品1，此时产品数量1
线程 消费者1 消费了产品1，此时产品数量0
线程 生产者1 生产了产品2，此时产品数量1
线程 生产者3 生产了产品3，此时产品数量2
线程 消费者2 消费了产品2，此时产品数量1
线程 消费者2 消费了产品3，此时产品数量0
线程 生产者1 生产了产品4，此时产品数量1
线程 消费者2 消费了产品4，此时产品数量0
线程 生产者1 生产了产品5，此时产品数量1
线程 消费者1 消费了产品5，此时产品数量0
线程 生产者3 生产了产品6，此时产品数量1
线程 生产者3 生产了产品7，此时产品数量2
线程 消费者2 消费了产品6，此时产品数量1
线程 生产者1 生产了产品8，此时产品数量2
线程 生产者2 生产了产品9，此时产品数量3
线程 消费者2 消费了产品7，此时产品数量2
线程 生产者2 生产了产品10，此时产品数量3
线程 消费者1 消费了产品8，此时产品数量2
线程 生产者1 生产了产品11，此时产品数量3
线程 消费者2 消费了产品9，此时产品数量2
线程 生产者2 生产了产品12，此时产品数量3
线程 消费者1 消费了产品10，此时产品数量2
线程 生产者1 生产了产品13，此时产品数量3
线程 消费者1 消费了产品11，此时产品数量2
线程 生产者3 生产了产品14，此时产品数量3
线程 消费者1 消费了产品12，此时产品数量2
线程 生产者2 生产了产品15，此时产品数量3
线程 消费者2 消费了产品13，此时产品数量2
线程 生产者2 生产了产品16，此时产品数量3
线程 消费者2 消费了产品14，此时产品数量2
线程 生产者1 生产了产品17，此时产品数量3
线程 消费者1 消费了产品15，此时产品数量2
线程 生产者3 生产了产品18，此时产品数量3
线程 消费者2 消费了产品16，此时产品数量2
线程 生产者2 生产了产品19，此时产品数量3
线程 消费者2 消费了产品17，此时产品数量2
线程 生产者1 生产了产品20，此时产品数量3
线程 消费者1 消费了产品18，此时产品数量2
线程 生产者3 生产了产品21，此时产品数量3
线程 消费者1 消费了产品19，此时产品数量2
线程 生产者2 生产了产品22，此时产品数量3
线程 消费者1 消费了产品20，此时产品数量2
线程 生产者1 生产了产品23，此时产品数量3
线程 消费者1 消费了产品21，此时产品数量2
线程 生产者2 生产了产品24，此时产品数量3
线程 消费者2 消费了产品22，此时产品数量2
线程 生产者3 生产了产品25，此时产品数量3
线程 消费者1 消费了产品23，此时产品数量2
线程 生产者2 生产了产品26，此时产品数量3
线程 消费者2 消费了产品24，此时产品数量2
线程 生产者1 生产了产品27，此时产品数量3
线程 消费者2 消费了产品25，此时产品数量2
线程 生产者3 生产了产品28，此时产品数量3
线程 消费者1 消费了产品26，此时产品数量2
线程 生产者3 生产了产品29，此时产品数量3
线程 消费者2 消费了产品27，此时产品数量2
线程 生产者2 生产了产品30，此时产品数量3
线程 消费者2 消费了产品28，此时产品数量2
线程 消费者1 消费了产品29，此时产品数量1
线程 消费者1 消费了产品30，此时产品数量0
```

