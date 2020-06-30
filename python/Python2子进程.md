# Python2子进程

python的subprocess模块在2和3中有一些不同，本文使用的是python2的

[官方文档](https://docs.python.org/zh-cn/2.7/library/subprocess.html)

推荐使用的是`subprocess.call`，如果无法满足需求，可以使用更底层的`subprocess.Popen`

### subprocess.call

call提供了非常多的参数，但是绝大部分会传递给subprocess.Popen执行

#### args

args是call的第一个参数，提供需要执行的命令

如果shell=True，推荐使用字符串，如果shell=False（即默认值），推荐使用序列

```python
subprocess.call(['ls','-l'])
subprocess.call('ls -l',shell=True)
```

#### shell

将shell设为True，将使用shell执行指定命令

当执行的命令来自不受信任的外部时，最好不要这样设置shell=True，因为很容易受到shell注入的攻击

### subprocess.Popen

