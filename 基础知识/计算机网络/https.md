# https

HTTPS: Hypertext Transfer Protocol Secure，即超文本传输安全协议，是HTTP的安全版

### 介绍

​		HTTP：直接通过明文在浏览器和服务器之间传递信息

​		HTTPS：采用对称加密和非对称加密结合的方式来保护浏览器和服务端之间的通信安全。对称加密算法加密数据，非对称加密算法交换密钥，数字证书验证身份

### 数据传输

​		HTTP：将报文直接传给传输层，套上TCP头后发出

​		HTTPS：先将报文传给SSL层进行加密，之后再传给传输层；目标主机收到报文后，从传输层出来后，先发给SSL层解密，再交给对应进程

### 加密、解密过程

1. 客户端发送 https 请求
2. 服务端返回 证书公钥
3. 客户端验证证书是否有效
   - 无效弹出警告框
4. 有效，生成一个随机值
5. 用证书的公钥加密随机值
6. 将加密后的密钥发送给服务端
7. 服务端用私钥解密密钥
8. 用密钥加密要发送的内容
9. 将加密后的内容发送给客户端
10. 用密钥解密信息

![img](https://upload-images.jianshu.io/upload_images/16749538-3ae48d5925636dc1.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)