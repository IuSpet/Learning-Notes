# JDBC

###### Java Database Connectivity 为多种关系型数据库提供统一访问方式

[TOC]

### 1.JDBC API：提供各种操作访问接口





### 2.JDBC API

#### 主要功能

- 与数据库建立连接


- 发送SQL语句

- 返回处理结果

```java
DriverManager //管理jdbc驱动

Connection //连接

Statement( PreparedSTratement)  //增删改查

CallableStatement //调用数据库中的存储过程/存储函数

Result  //返回的结果集
```
#### jdbc访问数据库的具体步骤：

1. ##### 导入驱动，加载具体的驱动类

   进入idea，选择preject settings，在dependencies选项卡下点击+导入jar包

   ```java
   Class.forName("com.mysql.jdbc.Driver");//加载mysql驱动类
   ```

   **8.0以上驱动语句和这个不一样**

2. ##### 与数据库建立连接

   ```java
   final String URL = "jdbc:mysql://123.57.42.253:3306/HHH";
   final String USERNAME = "root";
   final String PASS = "123456";
   conn = DriverManager.getConnection(URL,USERNAME,PASS);
   ```

3. ##### 发送SQL语句，执行

   

4. 处理结果集（查询）


### 3.各种数据库驱动：数据库厂商提供