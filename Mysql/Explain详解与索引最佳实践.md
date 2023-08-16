**1. Explain使用与详解**  
**1. Explain介绍**
使用EXPLAIN关键字可以模拟优化器执行SQL语句，分析你的查询语句或是结构的性能瓶颈
在 select 语句之前增加 explain 关键字，MySQL 会在查询上设置一个标记，执行查询会
返回执行计划的信息，而不是执行这条SQL。
**注意**： 如果 from 中包含子查询，仍会执行该子查询，将结果放入临时表中。

**Explain分析示例**

```
示例表：
2 DROP TABLE IF EXISTS `actor`;
3 CREATE TABLE `actor` (
4 `id` int(11) NOT NULL,
5 `name` varchar(45) DEFAULT NULL,
6 `update_time` datetime DEFAULT NULL,
7 PRIMARY KEY (`id`)
8 ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
9
10 INSERT INTO `actor` (`id`, `name`, `update_time`) VALUES (1,'a','2017‐12‐22
15:27:18'), (2,'b','2017‐12‐22 15:27:18'), (3,'c','2017‐12‐22 15:27:18');
11
12 DROP TABLE IF EXISTS `film`;
13 CREATE TABLE `film` (
14 `id` int(11) NOT NULL AUTO_INCREMENT,
15 `name` varchar(10) DEFAULT NULL,
16 PRIMARY KEY (`id`),
17 KEY `idx_name` (`name`)
18 ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
19
20 INSERT INTO `film` (`id`, `name`) VALUES (3,'film0'),(1,'film1'),(2,'film2');
21
22 DROP TABLE IF EXISTS `film_actor`;
23 CREATE TABLE `film_actor` (
24 `id` int(11) NOT NULL,
25 `film_id` int(11) NOT NULL,
26 `actor_id` int(11) NOT NULL,
27 `remark` varchar(255) DEFAULT NULL,
28 PRIMARY KEY (`id`),
29 KEY `idx_film_actor_id` (`film_id`,`actor_id`)
30 ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
31
32 INSERT INTO `film_actor` (`id`, `film_id`, `actor_id`) VALUES (1,1,1),(2,1,2),(3,2,1);
```


```
mysql> explain select * from actor;
```

![image.png](WEBRESOURCE37c9d2891740a56f83d038d5095f8be1)
在查询中的每个表会输出一行，如果有两个表通过 join 连接查询，那么会输出两行。

**explain 两个变种**
**1）explain extended**：会在 explain 的基础上额外提供一些查询优化的信息。紧随其后通过 show warnings 命令可以得到优化后的查询语句，从而看出优化器优化了什么。额外还有 filtered 列，是一个半分比的值，rows * filtered/100 可以估算出将要和 explain 中前一个表进行连接的行数（前一个表指 explain 中的id值比当前表id值小的表）。

```
1 mysql> explain extended select * from film where id = 1;
```
![image.png](WEBRESOURCEb130861774e37a3501ffcc1741df5111)

```
1 mysql> show warnings;
```
![image.png](WEBRESOURCEda93b0d61c9a0eeb8d060cc73bcef1cd)


**2）explain partitions**：相比 explain 多了个 partitions字段，如果查询是基于分区表
的话，会显示查询将访问的分区。


### **explain中的列**
接下来我们将展示 explain 中每个列的信息。

**1. id列**
id列的编号是 select 的序列号，有几个 select 就有几个id，并且id的顺序是按 select 出现的顺序增长的。
id列越大执行优先级越高，id相同则从上往下执行，id为NULL最后执行。
举例： id有1,2,3，则id为3的执行优先级最高。

**2. select_type列**
select_type 表示对应行是简单还是复杂的查询。

1）simple：简单查询。查询不包含子查询和union。

```
1 mysql> explain select * from film where id = 2;
```
![image.png](WEBRESOURCE2eca5ce26f741cd11e264d2ee6901dd7)
2）primary：复杂查询中最外层的 select。
3）subquery：包含在 select 中的子查询（不在 from 子句中）。
4）derived：衍生查询。包含在 from 子句中的子查询。MySQL会将结果存放在一个临时表中，也称为派生表（derived的英文含义）。
用这个例子来了解 primary、subquery 和 derived 类型


```
1 mysql> set session optimizer_switch='derived_merge=off'; #关闭mysql5.7新特性对衍生表的合
并优化
2 mysql> explain select (select 1 from actor where id = 1) from (select * from film where
id = 1) der;
```
![image.png](WEBRESOURCE7ee07033ce361346479e98439de23af6)


```
1 mysql> set session optimizer_switch='derived_merge=on'; #还原默认配置
```
5）union：在 union 中的第二个和随后的 select

```
1 mysql> explain select 1 union all select 1;
```
![image.png](WEBRESOURCE2c379cd2c3fbc9fd19f92d21702ac758)

**3. table列**
这一列表示 explain 的一行正在访问哪个表。
当 from 子句中有子查询时，table列是 <derivenN> 格式，表示当前查询依赖 id=N 的查询，于是先执行 id=N 的查询。
当有 union 时，UNION RESULT 的 table 列的值为<union1,2>，1和2表示参与 union 的 select 行id。

**4. type列**
这一列表示**关联类型或访问类型**，即MySQL决定如何查找表中的行，查找数据行记录的大概范围。
依次从最优到最差分别为：**system > const > eq_ref > ref > range > index > ALL(全局)**
一般来说，**得保证查询达到range级别，最好达到ref**。

**NULL**：mysql能够在优化阶段分解查询语句，在执行阶段用不着再访问表或索引。例如：在索引列中选取最小值，可以单独查找索引来完成，不需要在执行时访问表。如下：
```
1 mysql> explain select min(id) from film;
```
![image.png](WEBRESOURCE526bbeec87cf42906bbdfbf821d11de7)

**const, system**：mysql能对查询的某部分进行优化并将其转化成一个常量（可以看show warnings 的结果）。const通俗点就是说和查询一个常量一样很快。用于primary key 或 unique key 的所有列与常数比较时，所以表最多有一个匹配行，读取1次，速度比较快。**system是const的特例**，表里只有一条元组匹配时为system。
```
1 mysql> explain extended select * from (select * from film where id = 1) tmp;
```
![image.png](WEBRESOURCEcc274db7d27936315321bd9c3df960ea)
```
1 mysql> show warnings;
```
![image.png](WEBRESOURCE4eedfba49a357f4d8b60b9c655e4d42f)

**eq_ref**：primary key(主键) 或 unique key(唯一键) 索引的所有部分被连接使用 ，最多只会返回一条符合条件的记录。这可能是在const 之外最好的联接类型了，简单的 select 查询不会出现这种 type。如下：
```
1 mysql> explain select * from film_actor left join film on film_actor.film_id = film.id;
```
![image.png](WEBRESOURCEb638984faff383630fd9d2d50b72f780)

**ref**：相比 eq_ref，不使用唯一索引，而是使用普通索引或者唯一性索引的部分前缀，索引要和某个值相比较，可能会找到多个符合条件的行。
1. 简单 select 查询，name是普通索引（非唯一索引）
```
1 mysql> explain select * from film where name = 'film1';
```
![image.png](WEBRESOURCE73217c23fca6850085ac707010963f74)

2.关联表查询，idx_film_actor_id是film_id和actor_id的联合索引，这里使用到了film_actor的左边前缀film_id部分。如下：
```
1 mysql> explain select film_id from film left join film_actor on film.id = film_actor.film_id;
```
![image.png](WEBRESOURCEeeaca6d4b542fdf3c052155eac409f53)

**range**：范围扫描通常出现在 in(), between ,> ,<, >= 等操作中。使用一个索引来检索给定范围的行。

```
1 mysql> explain select * from actor where id > 1;
```
![image.png](WEBRESOURCE8853306484bf00981d4b5c24317126a8)


**index**：扫描全索引就能拿到结果，一般是扫描某个二级索引，这种扫描不会从索引树根节点开始快速查找，而是直接对二级索引的叶子节点遍历和扫描，速度还是比较慢的，这种查询一般为使用覆盖索引，二级索引一般比较小，所以这种通常比ALL快一些。如下：
```
1 mysql> explain select * from film;
```
![image.png](WEBRESOURCE75172399942a75c119bdb38e5df63fe5)


**ALL**：即全表扫描，扫描你的聚簇索引的所有叶子节点。**通常情况下这需要增加索引来进行优化了**。如下：
```
1 mysql> explain select * from actor;
```
![image.png](WEBRESOURCEc873d9aeff31927ead2a3afe402bb479)

**5. possible_keys列**
这一列显示查询**可能使用**哪些索引来查找。
explain 时可能出现 possible_keys 有列，而 key 显示 NULL 的情况，这种情况是因为表中数据不多，mysql认为索引
对此查询帮助不大，选择了全表查询。
如果该列是NULL，则没有相关的索引。在这种情况下，可以通过检查 where 子句看是否可以创造一个适当的索引来提
高查询性能，然后用 explain 查看效果。

**6. key列**
这一列显示mysql**实际采用**哪个索引来优化对该表的访问。
如果没有使用索引，则该列是 NULL。如果想强制mysql使用或忽视possible_keys列中的索引，
在查询中使用 force index、ignore index。

**7. key_len列**
这一列显示了mysql在索引里使用的字节数，通过这个值可以算出具体使用了索引中的哪些列。
举例来说，film_actor的联合索引 idx_film_actor_id 由 film_id 和 actor_id 两个int列组成，并且每个int是4字节。通过结果中的key_len=4可推断出查询使用了第一个列：film_id列来执行索引查找。


```
1 mysql> explain select * from film_actor where film_id = 2;
```
![image.png](WEBRESOURCE48809d9c3ebc347766bf93269ebf1661)
**key_len计算规则如下：**
- 字符串，char(n)和varchar(n)，5.0.3以后版本中，**n均代表字符数，而不是字节数**，如果是utf-8，一个数字或字母占1个字节，一个汉字占3个字节。
  -  char(n)：如果存汉字长度就是 3n 字节。
  -  varchar(n)：如果存汉字则长度是 3n + 2 字节，加的2字节用来存储字符串长度，因为
varchar是变长字符串。

- 数值类型
  -  tinyint：1字节
  -  smallint：2字节
  -  int：4字节
  -  bigint：8字节
  -  
- 时间类型
  -  date：3字节
  -  timestamp：4字节
  -  datetime：8字节

- 如果字段允许为 NULL，需要1字节记录是否为 NULL

索引最大长度是768字节，当字符串过长时，mysql会做一个类似左前缀索引的处理，将前半部分的字符提取出来做索引。

**8. ref列**
这一列显示了在key列记录的索引中，表查找值所用到的列或常量，常见的有：const（常量），字段名（例：film.id）

**9. rows列**
这一列是mysql估计要读取并检测的行数，**注意**这个不是结果集里的行数。

**10. Extra列**
这一列展示的是额外信息。常见的重要值如下：
**1）Using index**：使用覆盖索引。
**覆盖索引定义**：覆盖索引并不是一种索引，它是一种查询的方式，表示所查询的字段在索引树里都包含了。mysql执行计划explain结果里的key有使用索引，如果select后面查询的字段都可以从这个索引的树中获取，这种情况一般可以说是用到了覆盖索引，extra里一般都有using index；覆盖索引一般针对的是辅助索引，整个查询结果只通过辅助索引就能拿到结果，不需要通过辅助索引树找到主键，再通过主键去主键索引树里获取其它字段值。如下：film_id在索引树里包含了。
```
1 mysql> explain select film_id from film_actor where film_id = 1;
```
![image.png](WEBRESOURCE7d5cea43c907dcf9539697879c8abbd8)

**2）Using where**：使用 where 语句来处理结果，并且查询的列未被索引覆盖。
```
1 mysql> explain select * from film_actor where film_id > 1;
```
![image.png](WEBRESOURCE8663b6979961721ce2e43f235373ecd7)

**3）Using index condition**：查询的列不完全被索引覆盖，where条件中是一个前导列的范围。
```
1 mysql> explain select * from film_actor where film_id > 1;
```
![image.png](WEBRESOURCE6bda6d79dbb35d60d1f8c48e33b4c1cb)


**4）Using temporary**：mysql需要创建一张临时表来处理查询。出现这种情况一般是要进行优化的，首先是想到用索引来优化。

1. actor.name没有索引，此时创建了张临时表来distinct。
```
1 mysql> explain select distinct name from actor;
```
![image.png](WEBRESOURCE3a1d245394c4f594807df6ccdd008c91)

2. film.name建立了idx_name索引，此时查询时extra是using index,没有用临时表。
```
1 mysql> explain select distinct name from film;
```
![image.png](WEBRESOURCE8aa51d9893ad5c28a0b94362cfff206b)

**5）Using filesort**：将用外部排序而不是索引排序，数据较小时从内存排序，否则需要在磁盘完成排序。这种情况下一般也是要考虑使用索引来优化的。

1. actor.name未创建索引，会浏览actor整个表，保存排序关键字name和对应的id，然后排序name并检索行记录。
```
1 mysql> explain select * from actor order by name;
```
![image.png](WEBRESOURCEe1c51fa3841e5ded9ed6d166f1e65c8e)

2. film.name建立了idx_name索引,此时查询时extra是using index。
```
1 mysql> explain select * from film order by name;
```
![image.png](WEBRESOURCE7768741b7b90e3a66aa425ecc6e3afb3)

**6）Select tables optimized away**：使用某些聚合函数（比如 max、min）来访问存在索引的某个字段时。
```
1 mysql> explain select min(id) from film;
```
![image.png](WEBRESOURCE000e9194313a142f7a83d64c4fdf98c6)


### **索引最佳实践**
```
示例表：
2 CREATE TABLE `employees` (
3 `id` int(11) NOT NULL AUTO_INCREMENT,
4 `name` varchar(24) NOT NULL DEFAULT '' COMMENT '姓名',
5 `age` int(11) NOT NULL DEFAULT '0' COMMENT '年龄',
6 `position` varchar(20) NOT NULL DEFAULT '' COMMENT '职位',
7 `hire_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '入职时间',
8 PRIMARY KEY (`id`),
9 KEY `idx_name_age_position` (`name`,`age`,`position`) USING BTREE
10 ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8 COMMENT='员工记录表';
11
12 INSERT INTO employees(name,age,position,hire_time) VALUES('LiLei',22,'manager',NOW());
13 INSERT INTO employees(name,age,position,hire_time) VALUES('HanMeimei',
23,'dev',NOW());
14 INSERT INTO employees(name,age,position,hire_time) VALUES('Lucy',23,'dev',NOW());
```

**1.全值匹配**

```
1 EXPLAIN SELECT * FROM employees WHERE name= 'LiLei';
```
![image.png](WEBRESOURCE5ed6850299e3ef79ece0e92272dc5f84)

```
1 EXPLAIN SELECT * FROM employees WHERE name= 'LiLei' AND age = 22;
```
![image.png](WEBRESOURCE93e4bd155812ede8f3172e4467628357)

```
1 EXPLAIN SELECT * FROM employees WHERE name= 'LiLei' AND age = 22 AND position ='manager';
```
![image.png](WEBRESOURCE960409a383dd4951e4380b10ab716cf7)

**2.最左前缀法则**
如果索引了多列，要遵守最左前缀法则。指的是查询从索引的最左前列开始并且不跳过索引中的列。
```
1 EXPLAIN SELECT * FROM employees WHERE name = 'Bill' and age = 31;
2 EXPLAIN SELECT * FROM employees WHERE age = 30 AND position = 'dev';
3 EXPLAIN SELECT * FROM employees WHERE position = 'manager';
```
![image.png](WEBRESOURCEc96438987b4240a128fdf3abd29a3010)

**3.不在索引列上做任何操作（计算、函数、（自动or手动）类型转换），会导致索引失效而转向全表扫描**
```
1 EXPLAIN SELECT * FROM employees WHERE name = 'LiLei';
2 EXPLAIN SELECT * FROM employees WHERE left(name,3) = 'LiLei';
```
**备注：**
<1>. LEFT()函数是一个字符串函数,它返回具有指定长度的字符串的左边部分。LEFT(Str,length); 接收两个参数: str:一个字符串; length:想要截取的长度,是一个正整数。
<2>.Why聚合函数索引列不能走索引?
答：聚合函数改变了这个列的值，不能在索引树里匹配上，所以不能走索引了。

![image.png](WEBRESOURCEd0d598813b6fd71808ea96a0dbfd9151)
给hire_time增加一个普通索引：
```
1 ALTER TABLE `employees` ADD INDEX `idx_hire_time` (`hire_time`) USING BTREE ;
1 EXPLAIN select * from employees where date(hire_time) ='2018‐09‐30';
```
![image.png](WEBRESOURCE0b064d29b0568a2d452036c576afd8fc)
转化为日期范围查询，有可能会走索引：
```
1 EXPLAIN select * from employees where hire_time >='2018‐09‐30 00:00:00' and hire_time <='2018‐09‐30 23:59:59';
```
![image.png](WEBRESOURCEac514ca1a86b6cde38bb9d666ae029d3)
还原最初索引状态
```
1 ALTER TABLE `employees` DROP INDEX `idx_hire_time`;
```



**4.存储引擎不能使用索引中范围条件右边的列**
```
1 EXPLAIN SELECT * FROM employees WHERE name= 'LiLei' AND age = 22 AND position ='manage
r';
2 EXPLAIN SELECT * FROM employees WHERE name= 'LiLei' AND age > 22 AND position ='manage
r';
```
![image.png](WEBRESOURCEe6cf23beae7e87e96fa92d2a04d1afb4)
**现象解释**：第2个sql语句name和age走了索引，position没有走索引。
为什么？
答：联合索引中，在此语句中，name列的值是确定的，age是范围查询，按索引树的有序性可以得知是可以走索引了，但是此时age字段不相等，不能保证position的有序性，所以不走索引。

**5.尽量使用覆盖索引（只访问索引的查询（索引列包含查询列）），减少 select * 语句**
```
1 EXPLAIN SELECT name,age FROM employees WHERE name= 'LiLei' AND age = 23 AND position ='manager';
```
![image.png](WEBRESOURCE7d653fa56cc3982f29b317868d01431e)

```
1 EXPLAIN SELECT * FROM employees WHERE name= 'LiLei' AND age = 23 AND position ='manager';
```
![image.png](WEBRESOURCE262066588923aba6c570ab8616b7b3a9)

**6.mysql在使用不等于（！=或者<>），not in ，not exists 的时候无法使用索引会导致全表扫描**
< 小于、> 大于、<=、>= 这些，mysql内部优化器会根据检索比例、表大小等多个因素整体评估是否使用索引。

```
1 EXPLAIN SELECT * FROM employees WHERE name != 'LiLei';
```
![image.png](WEBRESOURCEa91311fbc68947c9ad86a9fadd60a76d)

**7. is null,is not null 一般情况下也无法使用索引**
```
1 EXPLAIN SELECT * FROM employees WHERE name is null
```
![image.png](WEBRESOURCE2f64960d9e439321ae7018f5a462bb59)

**8. like以通配符开头（'$abc...'）mysql索引失效会变成全表扫描操作**
```
1 EXPLAIN SELECT * FROM employees WHERE name like '%Lei'
```
![image.png](WEBRESOURCEab79e0f4f093dd6c3f65b1e9aeee83a8)
上图没有走索引。

```
1 EXPLAIN SELECT * FROM employees WHERE name like 'Lei%'
```
![image.png](WEBRESOURCEcb23aaf9f47218b72bd1c16973264da0)

**问题**：解决like'%字符串%'索引不被使用的方法？
a）使用覆盖索引，查询字段必须是建立覆盖索引字段
```
1 EXPLAIN SELECT name,age,position FROM employees WHERE name like '%Lei%';
```
![image.png](WEBRESOURCEca756af51a31fe808709737ee2d9b6a5)
查询字段少一点，最好是索引树内的。

b）如果不能使用覆盖索引则可能需要借助搜索引擎

**9.字符串不加单引号索引失效**
```
1 EXPLAIN SELECT * FROM employees WHERE name = '1000';
2 EXPLAIN SELECT * FROM employees WHERE name = 1000;
```
![image.png](WEBRESOURCEbecdb6acfe1378a545a6d03fdcd4ea26)
备注：被比较字段和要比较的值的类型最好相同。

**10.少用or或in，用它查询时，mysql不一定使用索引，mysql内部优化器会根据检索比例、表大小等多个因素整体评估是否使用索引，详见范围查询优化**
```
1 EXPLAIN SELECT * FROM employees WHERE name = 'LiLei' or name = 'HanMeimei';
```
![image.png](WEBRESOURCE4c436a50316f82b03d86225fe11eddfd)

**11.范围查询优化给年龄添加单值索引**
```
1 ALTER TABLE `employees` ADD INDEX `idx_age` (`age`) USING BTREE ;
1 explain select * from employees where age >=1 and age <=2000;
```
![image.png](WEBRESOURCEdf6b3376b0a6aa3d075ab4c4932bd744)
**没走索引原因**：mysql内部优化器会根据检索比例、表大小等多个因素整体评估是否使用索引。比如这个例子，可能是由于单次数据量查询过大导致优化器最终选择不走索引。
**优化方法**：可以将大的范围拆分成多个小范围。

```
1 explain select * from employees where age >=1 and age <=1000;
2 explain select * from employees where age >=1001 and age <=2000;
```
![image.png](WEBRESOURCEfb6fe1b413729f7005169122b2bc9f50)
还原最初索引状态
```
1 ALTER TABLE `employees` DROP INDEX `idx_age`;
```

**索引使用总结**：
![image.png](WEBRESOURCE7cea7dd857efad28282f2d703b6f77ed)
like KK%相当于=常量，%KK和%KK% 相当于范围

```
1 ‐‐ mysql5.7关闭ONLY_FULL_GROUP_BY报错
2 select version(), @@sql_mode;SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
```



































