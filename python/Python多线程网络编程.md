# Python多线程网络编程

​		完成一个签到程序，客户端向服务器发送一个姓名，服务器阻塞10s后，返回签到成功信息，多个客户端同时签到时不能排队

### server

```python
import socket
from datetime import datetime
from threading import Thread
from time import sleep


def reply(acptsock: socket.socket):
    data = acptsock.recv(2048).decode('ascii')
    res = 'Sign in - ' + data + ' - @' + str(datetime.now())
    print(res)
    sleep(10)
    acptsock.send(res.encode('utf-8'))


s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind(('127.0.0.1', 7887))
s.listen(10)
while True:
    print('wait to connect')
    sock, addr = s.accept()
    print(sock.getpeername(), 'connected successfully')
    Thread(target=reply, args=(sock,)).start()
```

### client

```python
import socket
import datetime

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
try:
    s.connect(('127.0.0.1', 7887))
except ValueError as e:
    print('valueError:', e)
except OSError as e:
    print('valueError:', e)
else:
    print('connect successfully')
    name = input().encode('utf-8')
    s.send(name)
    buff = s.recv(1024)
    print(buff.decode('utf-8'))
    print(datetime.datetime.now())
```

