## MySQL中数据类型(int char varchat datetime text)的含义，varchar和char的区别。

int: 数值
char: 字符
varchar: 字符串
datetime: 时间
text: 文本

varchar 是字符串，可存储可变长度的字符
char 是字符，只能存储定长的字符

## 主键索引和唯一索引的区别
一个表只能有一个主键索引，可以有多个唯一索引

主键索引一定是唯一索引，唯一索引不一定是主键索引

主键可以与外键构成参照完整性约束，防止数据不一致

## MySQL收到一条SQL语句后，又哪些执行过程
1. 连接，客户端连接到MySQL服务器，将SQL发送到服务端，服务端会做一些身份认证和获取权限的工作
2. 查询缓存，MySql 在执行查询时会先对查询缓存进行查询，是否之前执行过此查询，之前执行过的语句会以key-value形式，被直接缓存在内存当中key为语句，value是查询结果
3. 如果没有命中查询缓存，MySQL分析器开始对SQL语句进行词法分析&语法分析
4. 经过分析器之后，会经过MySQL的优化器，优化器决定是否使用索引及用哪个索引
5. 执行器真正执行sql语句

```bash
mysql> show profile for query 1;
+----------------------+----------+
| Status               | Duration |
+----------------------+----------+
| starting             | 0.002686 |
| checking permissions | 0.000020 |
| Opening tables       | 0.000015 |
| init                 | 0.000025 |
| System lock          | 0.000030 |
| optimizing           | 0.000006 |
| statistics           | 0.000012 |
| preparing            | 0.000011 |
| executing            | 0.000003 |
| Sending data         | 0.000158 |
| end                  | 0.000008 |
| query end            | 0.000009 |
| closing tables       | 0.000011 |
| freeing items        | 0.000033 |
| cleaning up          | 0.000021 |
+----------------------+----------+
15 rows in set, 1 warning (0.01 sec)
```

## 熟悉的Mysql存储引擎有哪些？区别是？

## mysql, redis, memcache，区别是什么

memcache更加快速，在读取性能上比 Redis 快，缺点是仅支持字符串。

Redis支持丰富的数据结构类型，字符串，散列（哈希），集合，有序集合，还支持订阅发布，地理位置等等。

实际运用中可以redis，memcache结合，memcache可作为session存储的方式，session都是KV类型键值对。

## explain的结果中，哪些查询参数是重要的？

select_type : 查询类型，有简单查询、联合查询、子查询等
key : 使用的索引
rows : 扫描的行数

## Mysql 事务的定义


## 知道哪些索引的类型？

1. 主键索引：主键索引是一种特殊的唯一索引，不允许有空值
2. 普通索引或者单列索引
3. 多列索引（复合索引）：复合索引指多个字段上创建的索引，只有在查询条件中使用了创建索引时的第一个字段，索引才会被使用。使用复合索引时遵循最左前缀集合
4. 唯一索引或者非唯一索引
5. 空间索引：空间索引是对空间数据类型的字段建立的索引，MYSQL中的空间数据类型有4种，分别是GEOMETRY、POINT、LINESTRING、POLYGON。

### MySQL主要提供2种方式的索引
#### B-Tree索引
B树索引具有范围查找和前缀查找的能力，对于有N节点的B树，检索一条记录的复杂度为O(LogN)。相当于二分查找，如果值的差异性相对较差，并且以范围查找为主，B树是更好的选择，它支持范围查找。
#### Hash索引
哈希索引只能做等于查找，但是无论多大的Hash表，查找复杂度都是O(1)，如果值的差异性大，并且以等值查找（=、 <、>、in）为主，Hash索引是更高效的选择，它有O(1)的查找复杂度。

## MySQL中索引是如何实现的？

## MySQL中哪些情况下索引会失效？

1. 单列索引无法存储 NULL 值， 复合索引无法存储全为 NULL 的值，查询时，如果使用is null条件，索引会失效
2. 前导模糊查询(%XX) 不能利用索引
3. 如果条件中有or，即使其中有条件带索引也不会使用，要想使用or，又想让索引生效，只能将or条件中的每个列都加上索引
4. 如果列类型是字符串，那一定要在条件中将数据使用引号引用起来,否则不使用索引
5. 不在索引列上做任何操作（计算，函数，（自动或者手动）类型装换），会导致索引失效而导致全表扫描。
6. 最佳左前缀法则——如果索引了多列，要遵守最左前缀法则。指的是查询要从索引的最左前列开始并且不跳过索引中的列


## 如下Mysql表，请标明以下select语句可以使用索引，能用到哪一组索引。

```SQL
create table t1 {
    name char(100),
    age int,
    sex int,
    addr char(100),
    index a(name, age, sex),
    index a2(addr)
} engine=innodb default charset=utf8
```

A. select * from t1 where name=x and age = x

B. select * from t1 where name=x;

C. select * from t1 where age=x and sex=x

D. select * from t1 where addr=x


## MySQL如何优化千万级的大表

第一优化你的sql和索引；

第二加缓存，memcached,redis；

第三以上都做了后，还是慢，就做主从复制或主主复制，读写分离，可以在应用层做，效率高，也可以用三方工具，第三方工具推荐360的atlas,其它的要么效率不高，要么没人维护；


第四如果以上都做了还是慢，不要想着去做切分，mysql自带分区表，先试试这个，对你的应用是透明的，无需更改代码,但是sql语句是需要针对分区表做优化的，sql条件中要带上分区条件的列，从而使查询定位到少量的分区上，否则就会扫描全部分区，另外分区表还有一些坑，在这里就不多说了；

第五如果以上都做了，那就先做垂直拆分，其实就是根据你模块的耦合度，将一个大的系统分为多个小的系统，也就是分布式系统；第六才是水平切分，针对数据量大的表，这一步最麻烦，最能考验技术水平，要选择一个合理的sharding key,为了有好的查询效率，表结构也要改动，做一定的冗余，应用也要改，sql中尽量带sharding key，将数据定位到限定的表上去查，而不是扫描全部的表；

## 以下有3个表
Score:

| id | student_id | subject_id | score |
|----|------------|------------|-------|
|  1 |     5      |     1      | 70    |
|  2 |     3      |      1     |  66   |
|  3 |     5      |     2      |  100  |
|  4 |     6      |     3      |  55   |
|  5 |     5      |     3      |  100  |
|  6 |     3      |     2      |    89 |

Subject:

| id | name |
|----|------|
| 1 | 科目一 |
|2 | 科目二 |
|3| 科目三 |

Student:

| id | name |
|----|------|
| 3 | 张三 |
|5 | 李四 |
|6| 王五 |

### 把王二的科目三成绩改为66分
```sql
UPDATE Score 
LEFT JOIN Subject ON Score.subject_id = Subject.id
LEFT JOIN Student ON Score.student_id = Student.id
SET Score.score = 66
WHERE Subject.name = '科目三' AND Student.name = '王二’
```


### 选出平均分 >= 60分的学生姓名


### 现有3中查询如下，新建一个索引能够同时提升以上三个查询的性能(最优索引)
```sql
select id from score where score = 100
select id from score where student_id = 5 and score = 100
select id from score where student_id = 5 and subject_id = 2 and score > 60
```