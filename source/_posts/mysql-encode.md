---
title: mysql 解决emoji插入问题
date: 2018-10-17 12:14:49
tags:
  - mysql
  - docker
categories:
  - 后端
---

### 原因

一般设置 mysql 字符时都会使用 utf8 字符编码，但是最近文本中插入 emoji 表情的时候遇到了麻烦，只要文本中有 emoji 表情存入数据库的时候就会报错。

### 解决方法

emoji 使用 4 个字节，utf8 一个字符最多三个字节，如果想使用 emoji 表情的话就得使用 utf8mb4 字符编码(扩展到一个字符 4 个字节)。

### 解决

首先查看表字段的字符集

```
show full columns from article;
```

显示内容

```
+-------------+--------------+-----------------+------+-----+---------+-------+---------------------------------+---------+
| Field       | Type         | Collation       | Null | Key | Default | Extra | Privileges                      | Comment |
+-------------+--------------+-----------------+------+-----+---------+-------+---------------------------------+---------+
| id          | varchar(16)  | utf8_general_ci | NO   | PRI | NULL    |       | select,insert,update,references |         |
| title       | varchar(255) | utf8_general_ci | NO   |     | NULL    |       | select,insert,update,references |         |
| content     | text         | utf8_general_ci | NO   |     | NULL    |       | select,insert,update,references |         |
| status      | int(11)      | NULL            | YES  |     | NULL    |       | select,insert,update,references |         |
| create_time | datetime     | NULL            | YES  |     | NULL    |       | select,insert,update,references |         |
| update_time | datetime     | NULL            | YES  |     | NULL    |       | select,insert,update,references |         |
| category_id | varchar(16)  | utf8_general_ci | NO   |     | NULL    |       | select,insert,update,references |         |
| seo_id      | varchar(16)  | utf8_general_ci | YES  |     | NULL    |       | select,insert,update,references |         |
| count       | int(11)      | NULL            | YES  |     | NULL    |       | select,insert,update,references |         |
+-------------+--------------+-----------------+------+-----+---------+-------+---------------------------------+---------+
```

<!--more-->

这里可以看到 content 的字符编码是 utf8 格式的，修改为 utf8mb4 格式

```
alter table article change content content text character set utf8mb4 collate utf8mb4_general_ci;
```

显示内容中 cotnent 格式边成了 utf8mb4

```
+-------------+--------------+--------------------+------+-----+---------+-------+---------------------------------+---------+
| Field       | Type         | Collation          | Null | Key | Default | Extra | Privileges                      | Comment |
+-------------+--------------+--------------------+------+-----+---------+-------+---------------------------------+---------+
| id          | varchar(16)  | utf8_general_ci    | NO   | PRI | NULL    |       | select,insert,update,references |         |
| title       | varchar(255) | utf8_general_ci    | NO   |     | NULL    |       | select,insert,update,references |         |
| content     | text         | utf8mb4_general_ci | YES  |     | NULL    |       | select,insert,update,references |         |
| status      | int(11)      | NULL               | YES  |     | NULL    |       | select,insert,update,references |         |
| create_time | datetime     | NULL               | YES  |     | NULL    |       | select,insert,update,references |         |
| update_time | datetime     | NULL               | YES  |     | NULL    |       | select,insert,update,references |         |
| category_id | varchar(16)  | utf8_general_ci    | NO   |     | NULL    |       | select,insert,update,references |         |
| seo_id      | varchar(16)  | utf8_general_ci    | YES  |     | NULL    |       | select,insert,update,references |         |
| count       | int(11)      | NULL               | YES  |     | NULL    |       | select,insert,update,references |         |
+-------------+--------------+--------------------+------+-----+---------+-------+---------------------------------+---------+
```

也可以直接修改表的编码格式

```
alter table article convert to character set utf8mb4 collate utf8mb4_general_ci;
```

显示内容中批量修改了字符编码

```
+-------------+--------------+--------------------+------+-----+---------+-------+---------------------------------+---------+
| Field       | Type         | Collation          | Null | Key | Default | Extra | Privileges                      | Comment |
+-------------+--------------+--------------------+------+-----+---------+-------+---------------------------------+---------+
| id          | varchar(16)  | utf8mb4_general_ci | NO   | PRI | NULL    |       | select,insert,update,references |         |
| title       | varchar(255) | utf8mb4_general_ci | NO   |     | NULL    |       | select,insert,update,references |         |
| content     | text         | utf8mb4_general_ci | YES  |     | NULL    |       | select,insert,update,references |         |
| status      | int(11)      | NULL               | YES  |     | NULL    |       | select,insert,update,references |         |
| create_time | datetime     | NULL               | YES  |     | NULL    |       | select,insert,update,references |         |
| update_time | datetime     | NULL               | YES  |     | NULL    |       | select,insert,update,references |         |
| category_id | varchar(16)  | utf8mb4_general_ci | NO   |     | NULL    |       | select,insert,update,references |         |
| seo_id      | varchar(16)  | utf8mb4_general_ci | YES  |     | NULL    |       | select,insert,update,references |         |
| count       | int(11)      | NULL               | YES  |     | NULL    |       | select,insert,update,references |         |
+-------------+--------------+--------------------+------+-----+---------+-------+---------------------------------+---------+
```

也可以通过命令查看数据库和表设置的字符编码

```
show create database `db_name`;

show create table article;
```

修改数据库的默认字符集

```
alter database `db_name` character set = utf8mb4 collate = utf8mb4_general_ci;
```

最后修改 mysql 的配置文件 `my.cnf`, 并重启 mysql

```
[client]
default-character-set=utf8mb4

[client]
default-character-set=utf8mb4

[mysqld]
character-set-client-handshake=false
character-set-server=utf8mb4
collation-server=utf8mb4_general_ci
```

检查修改

```
SHOW VARIABLES WHERE Variable_name LIKE 'character\_set\_%' OR Variable_name LIKE 'collation%';
```

如果修改后的内容显示使用了 utf8mb4，就可以使用 emoji 表情了

```
+--------------------------+--------------------+
| Variable_name            | Value              |
+--------------------------+--------------------+
| character_set_client     | utf8mb4            |
| character_set_connection | utf8mb4            |
| character_set_database   | utf8mb4            |
| character_set_filesystem | binary             |
| character_set_results    | utf8mb4            |
| character_set_server     | utf8mb4            |
| character_set_system     | utf8               |
| collation_connection     | utf8mb4_general_ci |
| collation_database       | utf8mb4_general_ci |
| collation_server         | utf8mb4_general_ci |
+--------------------------+--------------------+
```
