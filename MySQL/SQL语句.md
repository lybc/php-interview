## 关联查询

### 6中关联查询
- 交叉连接 (CROSS JOIN) 
    
    笛卡尔积，结果集会很大，没有意义，很少使用

    ```sql
    select * from A, B, (,C)
    // OR
    select * from a CROSS JOIN B (CROSS JOIN C)
    ```
- 内连接（INNER JOIN）
    
    多表中同时符合某种条件的数据记录的集合
    
    ```sql
    select * from A, B where A.id = B.id
    // or

    select * from A inner join B on A.id = B.id
    ```

    1. 等值连接: ON A.id = B.id
    
    2. 不等值连接: ON A.id > B.id
    
    3. 自连接: 自己连接自己，如 A.id = A.pid


- 外连接（LEFT JOIN/RIGNT JOIN）
    
    1. 左外连接：left outer join, 以左表为主，先查询出左表，按照 on 后的关联条件匹配右表，没有匹配到的用null填充，可以简写成left join

    2. 右外连接：与左外相似，以右表为主，先查询出右表，按on后的关联条件匹配左表，没有匹配到的用nULL填充
   

- 联合查询（UNION&UNION ALL）

    ```sql
    select * from A union select * from b
    ```

    把多个结果集集中在一起，union前的结果为基准，需要注意的是联合查询的列数要相等，相同的记录行会合并

    union all

    不会合并重复行，效率比union高，因为不会去重
    
- 全连接（FULL JOIN） MySQL中不支持
  
- 子查询
  
    不建议使用，效率不好把控
## 关联更新

```sql
update A, B 
set A.c1 = B.c1
A.c2 = B.c2
where A.id = B.id
```

```sql
update A INNER JOIN B ON A.id = B.id SET A.c1 = B.c1, A.c2=B.c2 WHERE ...
```

## 查询优化

针对优化SQL语句执行效率的方法进行整理
  
### 查找分析查询速度慢的原因
记录慢查询日志

分析查询日志

show profile: set profiling = 1, 服务器上执行的所有语句会检测消耗的时间，存到临时表中

show profiles

show profile for query 临时表ID

show status 会返回一些计数器，show global status 查看服务器级别的所有计数

有时根据这些计数，可以猜测出哪些操作代价较高或消耗时间多

show processlist，可以帮助我们分析是否有大量的线程处于不正常的状态

explain: 分析单条sql语句

## 优化查询过程中的数据访问
访问数据太多导致性能下降

确定程序是否检索大量超过需要的数据，可能是太多列或行

确认MySQL是否在分析大量的数据行

### 避免以下情况

查询过多不需要的记录，使用limit（分页）解决

多表关联返回全部列，指定A.ID, A.name 解决

总是取出全部列, 避免select *

重复查询相同的数据，可以缓存数据

是否在扫描额外的记录，使用索引

改变数据库表的结构，修改数据库表范式

重写SQL，让优化器可以以更优的方式查询

## 优化长难的查询语句
一个复杂的查询好还是多个简单的查询好？

因为MySQL内部扫描数据很快，但响应给用户较慢，理论上来说使用尽可能少的查询更好，但有些情况来说切分查询也很有必要

如：删除1000W数据，可以改为1000次删除100条数据

分解关联查询：可以将一条关联语句分解成多条，让数据库缓存的效率更高，执行单个查询可以减少锁的竞争，在应用层做关联可以更容易对数据库进行拆分

## 优化特定类型的查询语句

### 优化count() 查询

count(*) 中的 * 会忽略所有的列，直接统计所有的行数，因此不要使用count(列名)

MyIsam中没有条件的count(*) 非常快，有where条件是则不一定

可以使用explain查询近似值
增加汇总表
使用缓存

### 优化关联查询
确定on或者using字句的列上有索引

确保group by和order by中只有一个表中的列，这样MySQL才有可能使用索引


### 优化子查询

使用关联查询替代

### 优化group by和distinct

可以使用索引来优化，是最有效的优化方法

关联查询中使用标识列进行分组的效率更高

如不需要order by 进行group by时使用order by null，MySQL不会进行文件排序

### 优化limit分页

limit偏移量大的时候，查询效率低，可以记录上次查询的最大ID，下次查询直接根据ID来查

### 优化union
union all 效率高于union