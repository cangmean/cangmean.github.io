---
title: mysql 常用命令
date: 2018-07-24 19:30:44
tags:
  - mysql
categories:
  - 后端
---

因为项目中使用 orm， mysql 一些常用的命令都模糊了， 在此记录下 mysql 常用命令方便随时查看.

### 库操作

对库的一些操作

#### 创建数据库

```
create database `db_name` default character set utf8;
```

#### 删除数据库

```
drop database `db_name`;
```

### 表操作

对表的一些操作

#### 创建数据表

创建一个`user`的数据表

```
CREATE TABLE `user` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `username` varchar(50) DEFAULT '' COMMENT '用户名',
    `password` varchar(255) DEFAULT '' COMMENT '密码',
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

<!--more-->

#### 删除数据表

```
drop table `user`;
```

#### 插入数据

插入一条数据

```
insert into user(id, username, password) values(1, 'a', 'a123');
```

如果表名后面写字段名， 相应的 values 也必须对应上， 如果没有写字段名， 默认为全部字段.

```
insert into user(username, password) values('a', 'a123');
```

如果 values 后面写对组数据， 表示批量插入

```
insert into user values(1, 'a', 'a123'), (2, 'b', 'b123');
```

#### 查询数据

查询一条数据

```
select
    username
from
    user
where
    password like '%12%'
order by
    username desc;
```

#### 更新数据

```
update user set usrename='c' where id = 1;
```

#### 删除数据

```
delete from user where id = 1;
```

#### 添加字段

```
alter table user add `age` int(11) after username;
```

#### 修改字段

```
alter table user change `age` `create_time` varchar(8);
```

#### 修改字段类型

只修改字段类型，不修改名称时可使用

```
alter table user modify `age` varchar(2);
```

#### 删除字段

```
alter table user drop `age`;
```

#### 创建索引

创建普通索引, 注意`Null`值对索引无效， 还是会进行查询， 需要改成空字符串， 如果写入多个字段表示组合索引

```
alter table `表名` add index `索引名称` (`字段`， `字段`);
```

例:

```
alter table user add index `idx_name` (`username`);
```

创建唯一索引

```
alter table user add unique `idx_name` (`username`);
```

创建唯一组合索引

```
alter table user add unique `idx_name` (`username`, `password`);
```

#### 查看索引

```
show index from `user`;
```

#### 删除索引

```
alter table user drop index `idx_name`;
```

### 数据的导入导出

#### 导出操作

导出数据库数据

```
mysqldump -u root -p `数据库名` > db.sql
```

导出数据库指定表

```
mysqldump -u root -p `数据库名` `表名` > db.sql
```

导出所有数据库

```
mysqldump -u root -p --all-databases > all_db.sql
```

#### 导入操作

```
mysql -u root -p < db.sql
```
