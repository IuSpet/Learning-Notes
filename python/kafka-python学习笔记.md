# kafka-python学习笔记

[代码仓库](https://github.com/dpkp/kafka-python)

[官方文档](https://kafka-python.readthedocs.io/en/master/)

安装方式

```
pip install kafka-python
```

在使用这个包之前，需要正确安装kafka和zookeeper并且启动两个服务

### 生产者

启动一个生产者

```python
from kafka import KafkaProducer

producer = KafkaProducer(bootstrap_servers='localhost:9092')
```

有了生产者就可以产生不同topic的消息

```python
future = producer.send('topic1', b'hello kafka')
result = future.get(timeout=60) #会阻塞直到消息发出或者超时
```

调用send方法时，发送过程行为像守护线程（没仔细看源码），如果只调用send方法，后面没有其他代码，程序会直接退出，而消息并没有发出去

### 消费者

