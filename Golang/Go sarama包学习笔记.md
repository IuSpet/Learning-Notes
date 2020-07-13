# Go sarama包学习笔记

[代码仓库](https://github.com/Shopify/sarama)

[官方文档](https://godoc.org/github.com/Shopify/sarama)

sarama提供了纯Go编写的kafka客户端，最新发行版本支持Go和Kafka的最新两个版本

## 生产者

sarama提供异步生产者和同步生产者

异步生产者用管道接受信息，并且在背后异步地生产消息。异步生产者会尽可能的提高效率，是sarama推荐的选择

同步生产者提供一个阻塞的方法，直到消息被确认成功生产。显然效率上肯定不如异步生产者，且在某些配置中，确认的消息仍会丢失

### type AsyncProducer

```go
type AsyncProducer interface {

    // 关闭生产者，当Errors和Successes两个管道都被关闭时，这步操作才会完成。
    // 所以调用该方法后，要继续从两个管道中读取数据，耗尽管道内的消息
    AsyncClose()

    // Close shuts down the producer and waits for any buffered messages to be
    // flushed. You must call this function before a producer object passes out of
    // scope, as it may otherwise leak memory. You must call this before calling
    // Close on the underlying client.
    Close() error

    // 将要发送的消息写入这个方法的返回管道中
    Input() chan<- *ProducerMessage

    // 如果启用发送成功配置（默认关闭），成功递送消息后，成功信息会写入该管道
    Successes() <-chan *ProducerMessage

    // 错误会通过该管道告诉用户，也可以更改配置组织接收错误消息
    Errors() <-chan *ProducerError
  
  	// Successes和Errors管道的消息必须及时处理，否则阻塞后会导致死锁
```

使用AsyncProducer

```go
package main

import (
   "github.com/Shopify/sarama"
   "log"
   "os"
   "os/signal"
   "sync"
)

func main() {
   config := sarama.NewConfig()
   // 启用发送成功配置
   config.Producer.Return.Successes = true
   // 启动一个异步生产者
   producer, err := sarama.NewAsyncProducer([]string{"localhost:9092"}, config)
   if err != nil {
      panic(err)
   }
   log.SetOutput(os.Stdout)
   //接收终端的中断命令（ctrl C）来正常退出
   signals := make(chan os.Signal, 1)
   signal.Notify(signals, os.Interrupt)

   var (
      wg                          sync.WaitGroup
      enqueued, successes, errors int
   )

   // 开启Goroutine读取成功管道内的消息
   wg.Add(1)
   go func() {
      defer wg.Done()
      for range producer.Successes() {
         successes++
      }
   }()

   // 开启Goroutine读取错误管道内的消息
   wg.Add(1)
   go func() {
      defer wg.Done()
      for err := range producer.Errors() {
         log.Println(err)
         errors++
      }
   }()

ProducerLoop:
   for {
      // 消息指针
      message := &sarama.ProducerMessage{Topic: "topic1", Value: sarama.StringEncoder("hello sarama kafka")}
      select {
      // 发送消息
      case producer.Input() <- message:
         enqueued++
      // 正常退出
      case <-signals:
         producer.AsyncClose()
         break ProducerLoop
      }
   }
   //等待两个管道内的消息处理完
   wg.Wait()
   log.Printf("Successfully produced: %d; errors: %d; produced: %d\n", successes, errors, enqueued)
}
```

## 消费者

### type Consumer

```go
type Consumer interface {
    // 返回可获得的主题集合
    Topics() ([]string, error)

    // 按给定主题返回按分区id排序后的切片
    Partitions(topic string) ([]int32, error)

    // 对于指定的主题、分区、偏移量创建一个分区消费者
    ConsumePartition(topic string, partition int32, offset int64) (PartitionConsumer, error)

    // HighWaterMarks returns the current high water marks for each topic and partition.
    // Consistency between partitions is not guaranteed since high water marks are updated separately.
    HighWaterMarks() map[string]map[int32]int64

    // 关闭消费者，必须在所有子分区消费者关闭后才可以调用
    Close() error
}
```

使用Consumer对象

```go
package main

import (
   "github.com/Shopify/sarama"
   "log"
   "os"
   "os/signal"
)

func main() {
   // 创建消费者对象
   consumer, err := sarama.NewConsumer([]string{"localhost:9092"}, nil)
   if err != nil {
      panic(err)
   }

   // 程序运行结束时，调用Close关闭消费者对象
   defer func() {
      if err := consumer.Close(); err != nil {
         log.Fatalln(err)
      }
   }()
   // 创建消费者对象管理的分区消费者对象
   partitionConsumer, err := consumer.ConsumePartition("topic1", 0, sarama.OffsetNewest)
   if err != nil {
      panic(err)
   }

   // 程序运行结束时，调用Close关闭分区消费者对象
   defer func() {
      if err := partitionConsumer.Close(); err != nil {
         log.Println(err)
      }
   }()

   //用系统中断信号作为结束程序的信号
   signals := make(chan os.Signal, 1)
   signal.Notify(signals, os.Interrupt)

   consumed := 0
ConsumerLoop:
   for {
      select {
      // 消费者从分区中拿出数据消费
      case msg := <-partitionConsumer.Messages():
         log.Printf("Consumed message offset %d\n", msg.Offset)
         consumed++
      // 收到中断信号结束程序
      case <-signals:
         break ConsumerLoop
      }
   }
}
```