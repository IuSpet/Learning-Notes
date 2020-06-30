# Hive笔记

### 什么是hive

1. hive是一个基于hadoop的数据仓库工具
2. 可以将结构化的数据映射为一张数据库表
3. 底层数据存储在HDFS上
4. 使用类SQL语句可以进行hive查询
5. hive本质是将SQL语句转换为MapReduce执行

### Hive的数据组织

1. hive的存储结构包括数据库、表、视图、分区和表数据。数据库、表、分区都对应HDFS上的一个目录，表数据对应HDFS对应目录下的文件
2. hive中的所有数据都存储在HDFS中，没有专门的数据存储格式
3. hive包含以下数据模型
   - database：在HDFS中是一个目录
   - table：database目录下的一个目录
   - external table：和table类似，不过存储位置可以指定任意HDFS目录路径
   - partition：分区，在HDFS中是table目录下的一个目录
   - bucket：在HDFS中表现为同一个表目录或者分区目录下根据某个字段的值进行hash散列后的多个文件
   - view：与传统数据库类似，只读，基于基本表创建

4. hive的元数据存储在RDBMS中，除元数据的所有数据都基于HDFS存储

### 内部表和外部表的区别

- 删除内部表，删除表元数据和数据
- 删除外部表，删除元数据，不删除数据

