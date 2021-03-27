---
title: "MySQL优化:回表查询和覆盖索引"
date: 2020-06-29T01:10:16+08:00
typora-root-url: ..\..\static
tags: ["mysql优化"]
categories: ["Mysql"]
draft: false
---

今天看mysql优化时,发现这个知识点有点模糊了,就重新整理下

## 回表是什么?

简单来说, 执行一条sql语句, 如果查询条件为`普通索引`(非聚簇cu索引),需要扫描`两次`B+树,第一次扫描通过普通索引定位到聚簇索引的值(也就是`主键`),然后第二次扫描通过聚簇索引的值定位到要查找的行记录数据

首先先解释下两大类索引:①聚簇索引 ②普通索引

### 聚簇索引:

> 1. 如果表设置了主键，则主键就是聚簇索引
> 2. 如果表没有主键，则会默认第一个NOT NULL，且唯一（UNIQUE）的列作为聚簇索引
> 3. 以上都没有，则会默认创建一个隐藏的row_id作为聚簇索引
> 4. InnoDB的聚簇索引的叶子节点存储的是行记录（其实是页结构，一个页包含多行数据），InnoDB必须要有至少一个聚簇索引。
>
> 由此可见，使用聚簇索引查询会很快，因为可以直接定位到行记录。

### 普通索引:

> 普通索引也叫二级索引，除聚簇索引外的索引，即非聚簇索引。
>
> InnoDB的普通索引叶子节点存储的是主键（聚簇索引）的值，而MyISAM的普通索引存储的是记录指针。



### 举个例子:

表结构:

```
mysql> create table user(
    -> id int(10) auto_increment,
    -> name varchar(30),
    -> age tinyint(4),
    -> primary key (id),
    -> index idx_age (age)
    -> )engine=innodb charset=utf8mb4;
```

id为聚簇索引,age为普通索引,当我们执行sql语句:
`select * from user where id = 1;`
查询条件为主键,也叫做`聚簇索引`时,只需要扫描一次B+树即可找到对应的数据,这时结构为

![1](/images/mysql-optimization-return-table-query-and-overlay-index.assets/16@I4UFGOCC99NKTSJJ.png)

当我们sql语句为:
`select * from user where age = 30;`
查询条件为`普通索引`,则需要通过普通索引 age=30 找到主键值为 1,再通过聚簇索引 id=1找到对应的行数据,结构为:

![2](/images/mysql-optimization-return-table-query-and-overlay-index.assets/295EG51V176T__X.png)

那怎么避免回表查询呢? 这时我们可以使用覆盖索引

> 覆盖索引:
>
> 只需要在一棵索引树上就能获取SQL所需的所有列数据，无需回表，速度更快。
> 例如：`select id,age from user where age = 10;`



### 如何实现覆盖索引:

`sql：select id,age,name from user where age = 10;`

分析：age是普通索引，但name列不在索引树上，所以通过age索引在查询到id和age的值后，需要进行回表再查询name的值。所以我们需要将name和age组成组合索引

`drop index idx_age on user;`

`create index idx_age_name on user(age,name);`