## MySQL中数据类型(int char varchat datetime text)的含义，varchar和char的区别。

int: 数值
char: 字符
varchar: 字符串
datetime: 时间
text: 文本

varchar 是字符串，可存储可变长度的字符
char 是字符，只能存储定长的字符

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

