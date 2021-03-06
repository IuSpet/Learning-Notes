# B+树索引

**没有索引时的搜索情况**

遍历所有索引页，如果搜索条件是主键，可以查页目录，否则只能再次遍历每一页内的所有记录

查看每个索引页时，伴随磁盘io，开销非常大

**优化方案**

建立一个目录快速查找索引页

在InnoDB管理下，这样的目录就叫索引

### InnoDB的索引方案

#### 目录项

查找主键所在页的目录索引项，存储格式和普通记录相同，`record_type`为1

目录项存储的数据只有两条，主键（聚簇索引是主键，其他索引是别的字段）和页号，由主键可以找到下一层索引页

#### 聚簇索引

各索引页组合成一棵B+树，这棵B+树就是一个索引，它有两个特点

1. 使用记录主键值的大小进行记录和页的排序
2. B+树叶子节点存储的是完整的用户记录

具有这两种特征的B+树叫做聚簇索引

#### 二级索引

用主键以外的字段按相同的规则（使用该字段的大小进行记录和页的排序）建立一颗B+树，在叶子节点中，存的不是真实记录，而是该字段与主键，通过该索引查找到对应记录到主键后，再到聚簇索引中再查一遍（回表），就可以找到对应记录

在记录时，会带着主键，保证唯一性；在比较过程中，如果索引字段相同，会比较主键

#### 联合索引

同时以多个字段的大小作为排序规则（按顺序比较，相同时比较下一个字段），建立B+树

### MySQL创建和删除索引

创建表的时候建立索引

```mysql
CREATE TABLE 表名 (
		列信息
		[KEY|INDEX] 索引名 (需要被索引的单个列或多个列)
);
```

修改表结构时添加索引

```mysql
ALTER TABLE 表名 ADD [INDEX|KEY] 索引名 (需要被索引的单个列或多个列);
```

修改表结构时删除索引

```mysql
ALTER TABLE 表名 DROP [INDEX|KEY] 索引名;
```

### 使用索引的代价

- 空间代价

每建立一个索引都要重建一颗B+树，会占用很大存储空间

- 时间代价

进行增、删、改操作时，需要维护各个索引B+树，索引越多，建立的B+树越多，时间负担越大

### 使用索引的条件

#### 全值匹配

搜索条件中的字段和索引的字段一致的情况称为全值匹配，会使用索引进行搜索

#### 匹配左边的列

使用联合索引时，假设索引字段为(field1,field2,field3)

因为建立索引时，比较顺序为field1,field2,field3

所以当比较条件中有左边的列时，可以使用索引；但如果没有field1，无法使用索引

#### 匹配列前缀

对字符串字段建立索引，会按照字典序进行比较，所以保证了前缀的有序性

在查询时，对只有前缀的模糊查询，如'bac%'也能使用索引

#### 匹配范围值

因为索引是有序的，所以匹配范围值时，如果匹配条件在索引上有序也会使用索引