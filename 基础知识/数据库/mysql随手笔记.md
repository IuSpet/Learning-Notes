# mysql小册笔记

查看表结构

```mysql
SHOW CREATE TABLE <表名>
```

### 系统变量

查看mysql默认系统变量

```mysql
# 查看全部
SHOW VARIABLES;
# 查看具体某一条
SHOW VARIABLES LIKE 'default_storage_engine';
# 用通配符进行模糊查询
SHOW VARIABLES LIKE 'default%';
# 默认查看session变量，加GLOBAL关键字后可查看全局的
SHOW VARIABLES LIKE 'default%';
```

修改musql系统变量

```mysql
# 设置全局系统变量
SET GLOBAL <变量名> = <新值>;
SET @@GLOBAL.<变量名> = <新值>;
# 设置当前会话（连接）系统变量
SET SESSTION <变量名> = <新值>;
SET @@SESSION.<变量名> = <新值>;
SET <变量名> = <新值>;
```

### 状态变量

mysql服务器维护的关于程序运行状态的变量

#### 查看状态变量

不行范围默认是session

```mysql
SHOW [GLOBAL|SESSION] STATUS [LIKE 匹配的模式];
```

### 字符集和比较规则

mysql一共有四个等级的字符集和比较规则

- 服务器级
- 数据库级
- 数据表级
- 数据字段级

在新建数据库、数据表和数据字段时，可以指定使用的字符集和比较规则，如果不显示指定，会默认使用上一级的规则

mysql客户端在和服务端通信时，使用的字符集会进行切换，伴随解码与重新编码，从mysql系统变量中可以产看对应的设置

- character_set_client——服务器解码请求时使用的字符集
- character_set_connection——服务器处理请求时会把字符串从`character_set_client`转换为`character_set_connection`
- character_set_results——服务器向客户端返回数据时使用的字符集

具体过程为，服务端拿到客户端的请求后，按`charater_set_client`解码，再编码为`character_set_connection`字符集，对应各列的信息再转换为对应列的字符集。查询结果转换为`character_set_results`后解码发给客户端

## InnoDB引擎

真实数据再不同引擎中存放的格式一般是不同的

### InnoDB行格式

InnoDB目前有4种行格式

- Compact
- Redundant
- Dynamic
- Compressed

#### Compact

compact格式中，一条完整的记录被分为`记录的额外信息`和`记录的真实数据`

##### 记录的额外信息

- 变长字段长度列表，逆序存放各变长字段数据占用的字节数（varchar、text、blob等），
- NULL值列表：用一个bit来代表一列是否为NULL，1代表NULL，0代表不为NULL。从该记录允许有NULL值（即没有用NOT NULL修饰）的最后一个字段开始逆序记录，最后在高位补0补成整数字节
- 记录头信息：固定5个字节，内容为

| 名称         | 大小 | 描述                                                         |
| ------------ | ---- | ------------------------------------------------------------ |
| 预留位1      | 1    | 暂无作用                                                     |
| 预留位2      | 1    | 暂无作用                                                     |
| delete_mask  | 1    | 标记该记录是否被删除                                         |
| min_rec_mask | 1    | B+树的每层非叶子节点中的最小记录都会添加该标记               |
| n_owned      | 4    | 表示当前记录拥有的记录数                                     |
| heap_no      | 13   | 表示当前记录在记录堆中的位置信息                             |
| record_type  | 3    | 表示当前记录的类型，0表示普通记录、1表示B+树非叶子节点记录、<br/>2表示最小记录、3表示最大记录 |
| next_record  | 16   | 表示下一条记录的相对位置                                     |

##### 记录的真实数据

除了记录各个不为NULL的数据外，MySQL会为每个记录添加一些列（也称为隐藏列）

- DB_ROW_ID(可选)：占6个字节，如果记录没有主键，也没有被Unique修饰的字段，mysql会添加该列作为主键
- DB_TRX_ID：占6个字节，事务ID
- DB_ROLL_PTR：占7个字节，回滚指针

其余列，固定长度字段使用固定空间存储，变长字段使用前面记录的长度存储，比较特殊的是char

对于char(n)类型的字段，当该列字符集对应的每个字符长度一定时，该类型算作固定长度的类型；当字符集中每个字符长度不定时，改类型算作变长类型，但是至少占据n字节的空间

#### Redundant

Redundant在MySQL5.0之前使用

行格式与Compact类似，分为额外信息和真实数据

额外信息分为**字段长度偏移列表**和**记录头信息**

字段长度偏移列表记录各个字段结束时的相对位置，以字节为单位

字段偏移量的第一位做NULL标志位，为1代表该记录为NULL。所以一个字节表示偏移量时，最多用7位，表示127，当记录总长度大于127时，每条字段偏移量改为2个字节存储，在记录头信息中有标志位记录使用1字节还是2字节表示偏移量

当记录是NULL是，定长字段仍占据相应空间，变长字段占据空间为0

与Compact不同，char(n)类型不再根据字符集占据最小空间，始终占据最大空间。即对于utf8mb4字符集的char(10)的字段，在Compact格式中最小占据10字节，在Redundant中始终占据40字节

**对Compact和Redundant行格式来说，当发生行溢出时（一页16kb存储不下两条记录），会将一部分数据存储在其他页中，在该记录的真实数据区域留20字节存储这些页的地址等信息**

#### Dynamic和Compressed

MySQL5.7默认使用Dynamic行格式

这两个行格式类似Compact，不同点是在行溢出时，会把所有字节都放到其他页中，只在记录的真实数据存储其他页面的地址

Compressed和Dynamic不同的是，Compressed行格式会采用压缩算法对页面进行压缩以节省空间

### InnoDB索引页结构

页是InnoDB管理存储空间的基本单位，大小是16KB，根据功能不同，分为多种不同的页。存储数据的页叫索引页

<table>
<thead>
<tr>
<th style="text-align:center">名称</th>
<th style="text-align:center">中文名</th>
<th style="text-align:center">占用空间大小</th>
<th style="text-align:center">简单描述</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:center"><code>File Header</code></td>
<td style="text-align:center">文件头部</td>
<td style="text-align:center"><code>38</code>字节</td>
<td style="text-align:center">页的一些通用信息</td>
</tr>
<tr>
<td style="text-align:center"><code>Page Header</code></td>
<td style="text-align:center">页面头部</td>
<td style="text-align:center"><code>56</code>字节</td>
<td style="text-align:center">数据页专有的一些信息</td>
</tr>
<tr>
<td style="text-align:center"><code>Infimum + Supremum</code></td>
<td style="text-align:center">最小记录和最大记录</td>
<td style="text-align:center"><code>26</code>字节</td>
<td style="text-align:center">两个虚拟的行记录</td>
</tr>
<tr>
<td style="text-align:center"><code>User Records</code></td>
<td style="text-align:center">用户记录</td>
<td style="text-align:center">不确定</td>
<td style="text-align:center">实际存储的行记录内容</td>
</tr>
<tr>
<td style="text-align:center"><code>Free Space</code></td>
<td style="text-align:center">空闲空间</td>
<td style="text-align:center">不确定</td>
<td style="text-align:center">页中尚未使用的空间</td>
</tr>
<tr>
<td style="text-align:center"><code>Page Directory</code></td>
<td style="text-align:center">页面目录</td>
<td style="text-align:center">不确定</td>
<td style="text-align:center">页中的某些记录的相对位置</td>
</tr>
<tr>
<td style="text-align:center"><code>File Trailer</code></td>
<td style="text-align:center">文件尾部</td>
<td style="text-align:center"><code>8</code>字节</td>
<td style="text-align:center">校验页是否完整</td>
</tr>
</tbody>
</table>
#### User Records

数据存放在`User Records`中

新申请的索引页中，`User Records`部分的大小为0，当要插入纪录时，会从`Free Space`中申请空间作为`User Records`的内容，当`Free Space`的空间用完后，也意味着这个页装满了，需要申请新的索引页

在InnoDB的行格式中，头信息的各个字段就是用来描述该记录在索引页中的一些属性

- delete_mask

这个字段标记当前记录是否被删除，1代表被删除，0代表没有

因为释放、申请空间的开销很大，所以删除记录时，会先将该字段标记为1，所有被删除的记录连成链表，便于复用这部分空间

- min_rec_mask

B+树的每层非叶子节点中的最小记录会添加该标记

- n_owned

每个组内最大记录的该属性有效，记录该组内记录数

- heap_no

这个属性表示当前记录在本页中的位置

在索引页申请后，会自动添加两条记录，最小记录和最大纪录，这两条记录的`heap_no`分别是0和1，所以其他插入的记录的`heap_no`值从2开始

最小记录和最大纪录存放在固定的空间中，记录之间的大小关系遵从主键大小比较

- record_type

这个属性表示记录的类型，0表示普通记录，1表示B+树非叶节点记录，2表示最小记录，3表示最大纪录

- next_record

这个属性表示从当前记录的真实数据到下一条记录的真实数据到地址偏移量

通过该属性，将各条记录连成链表，指针指向行的真实数据，向前可以看行的头信息，向后可以看数据

下一条记录指按照主键从小到大排序的下一条记录

第一条记录和最后一条记录分别是页中自动生成的最小记录和最大纪录

#### Page Directory

`Page Directory`中存放slot

##### 分组

对于页面内的记录，为了便于查找，InnoDB会对记录进行分组

每组中的最大记录，会将其地址偏移量（从页头开始算起）存放到`Page Directory`中，即slot；除此之外，该条记录的`n_owned`属性会记录该组内的记录数量，包括自身

分组的条数规则为：

- 最小记录所在的分组只能有1条记录

- 最大纪录所在的分组拥有的记录数在1-8之间

- 其余分组的条数在4-8之间

插入记录与分组的过程为：

- 索引页刚申请完，有两条记录，最小记录和最大纪录，分别属于两个分组，`Page Directory`中也会有两个slot
- 每插入一条记录，会与`Page Directory`各个slot对应的记录的主键值进行对比，插入恰好大于本记录的slot对应的分组内
- 如果插入后组内记录数大于8，会拆分成两个组，条数分别为4、5，这个过程会往`Page Directory`中插入一个新的slot

##### 加入分组后的查找过程

如果要查找主键为某一值的记录

在没有分组时，只能沿着记录链表一一比较

分组后，可以先在`Page Directory`中用二分法找到该记录所属的分组，再沿着链表一一比较该分组内的所有记录

#### Page Header

<table>
<thead>
<tr>
<th style="text-align:center">名称</th>
<th style="text-align:center">占用空间大小</th>
<th style="text-align:center">描述</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:center"><code>PAGE_N_DIR_SLOTS</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">在页目录中的槽数量</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_HEAP_TOP</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">还未使用的空间最小地址，也就是说从该地址之后就是<code>Free Space</code></td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_N_HEAP</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">本页中的记录的数量（包括最小和最大记录以及标记为删除的记录）</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_FREE</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">第一个已经标记为删除的记录地址（各个已删除的记录通过<code>next_record</code>也会组成一个单链表，这个单链表中的记录可以被重新利用）</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_GARBAGE</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">已删除记录占用的字节数</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_LAST_INSERT</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">最后插入记录的位置</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_DIRECTION</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">记录插入的方向</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_N_DIRECTION</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">一个方向连续插入的记录数量</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_N_RECS</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">该页中记录的数量（不包括最小和最大记录以及被标记为删除的记录）</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_MAX_TRX_ID</code></td>
<td style="text-align:center"><code>8</code>字节</td>
<td style="text-align:center">修改当前页的最大事务ID，该值仅在二级索引中定义</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_LEVEL</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">当前页在B+树中所处的层级</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_INDEX_ID</code></td>
<td style="text-align:center"><code>8</code>字节</td>
<td style="text-align:center">索引ID，表示当前页属于哪个索引</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_BTR_SEG_LEAF</code></td>
<td style="text-align:center"><code>10</code>字节</td>
<td style="text-align:center">B+树叶子段的头部信息，仅在B+树的Root页定义</td>
</tr>
<tr>
<td style="text-align:center"><code>PAGE_BTR_SEG_TOP</code></td>
<td style="text-align:center"><code>10</code>字节</td>
<td style="text-align:center">B+树非叶子段的头部信息，仅在B+树的Root页定义</td>
</tr>
</tbody>
</table>

#### File Header

<table>
<thead>
<tr>
<th style="text-align:center">名称</th>
<th style="text-align:center">占用空间大小</th>
<th style="text-align:center">描述</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:center"><code>FIL_PAGE_SPACE_OR_CHKSUM</code></td>
<td style="text-align:center"><code>4</code>字节</td>
<td style="text-align:center">页的校验和（checksum值）</td>
</tr>
<tr>
<td style="text-align:center"><code>FIL_PAGE_OFFSET</code></td>
<td style="text-align:center"><code>4</code>字节</td>
<td style="text-align:center">页号</td>
</tr>
<tr>
<td style="text-align:center"><code>FIL_PAGE_PREV</code></td>
<td style="text-align:center"><code>4</code>字节</td>
<td style="text-align:center">上一个页的页号</td>
</tr>
<tr>
<td style="text-align:center"><code>FIL_PAGE_NEXT</code></td>
<td style="text-align:center"><code>4</code>字节</td>
<td style="text-align:center">下一个页的页号</td>
</tr>
<tr>
<td style="text-align:center"><code>FIL_PAGE_LSN</code></td>
<td style="text-align:center"><code>8</code>字节</td>
<td style="text-align:center">页面被最后修改时对应的日志序列位置（英文名是：Log Sequence Number）</td>
</tr>
<tr>
<td style="text-align:center"><code>FIL_PAGE_TYPE</code></td>
<td style="text-align:center"><code>2</code>字节</td>
<td style="text-align:center">该页的类型</td>
</tr>
<tr>
<td style="text-align:center"><code>FIL_PAGE_FILE_FLUSH_LSN</code></td>
<td style="text-align:center"><code>8</code>字节</td>
<td style="text-align:center">仅在系统表空间的一个页中定义，代表文件至少被刷新到了对应的LSN值</td>
</tr>
<tr>
<td style="text-align:center"><code>FIL_PAGE_ARCH_LOG_NO_OR_SPACE_ID</code></td>
<td style="text-align:center"><code>4</code>字节</td>
<td style="text-align:center">页属于哪个表空间</td>
</tr>
</tbody>
</table>

对于索引页来说，一个索引页肯定不够存放所有数据，靠`FIL_PAGE_PREV`和`FIL_PAGE_NEXT`两个属性将多个索引页连成一个双向链表存储记录

#### File Trailer

这部分用来校验页有没有完整同步到磁盘上

一共占8字节，前4字节是校验和，与头部的对应，后4字节代表页面被最后修改时对应的日志序列位置