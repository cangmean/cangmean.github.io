---
title: 基于docker的mysql主从配置
date: 2018-07-13 11:12:01
tags: ["mysql", "docker"]
category: 后端
---

### 目的

备份数据, 主库挂了从库依然可以访问

### 使用 docker 生成镜像

本文默认为已安装 docker 环境, ubuntu 环境

```bash
在本机上安装mysql5.7版本
sudo docker pull mysql:5.7

# code 在code目录下创建mysql/master和mysql/slave目录
mkdir -p mysql/{master,slave}
```

然后在 master 和 slave 目录中写入配置文件`Dockerfile`和`my.cnf`

```
# master/Dockerfile和slave/Dockerfile
FROM mysql:5.7
COPY my.cnf /etc/mysql/
EXPOSE 3306
CMD ["mysqld"]

# master/my.cnf， 主库和从库的server-id不能相同
[mysqld]
log-bin=mysql-bin
server-id=1

# slave/my.cnf
log-bin=mysql-bin
server-id=2
```

分别在目录中执行以下命令生成镜像

<!--more-->

```bash
# master, . 表示当前目录
sudo docker build -t master/mysql .

# slave
sudo docker build -t slave/mysql .
```

### 创建并配置容器

创建容器初始化数据库密码

```bash
sudo docker run -p 3307:3306 --name mysql-master -e MYSQL_ROOT_PASSWORD=654321 -d master/mysql
sudo docker run -p 3308:3306 --name mysql-slave -e MYSQL_ROOT_PASSWORD=654321 -d slave/mysql
```

可以通过`sudo docker ps 查询`

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
b69cb0f6d536        slave/mysql         "docker-entrypoint.s…"   2 hours ago         Up 2 hours          0.0.0.0:3308->3306/tcp   mysql-slave
310f9101ef10        master/mysql        "docker-entrypoint.s…"   2 hours ago         Up 2 hours          0.0.0.0:3307->3306/tcp   mysql-master
```

写一个查询容器`IP地址`的 sh, 把内容复制到.bashrc 中

```
function docker_ip(){
  sudo docker inspect --format '{{ .NetworkSettings.IPAddress }}' $1
}
```

查看两个容器服务的`IP地址`, 连接数据库

```bash
# 查询地址
docker_ip mysql-master  # 172.17.0.2
docker_ip mysql-slave   # 172.17.0.3

# 连接数据库
mysql -u root -h 172.17.0.2 -p
mysql -u root -h 172.17.0.3 -p
```

在主库上进行配置

```
# 设置user用户并设置所有host可以访问, 访问密码654321
GRANT SELECT, REPLICATION SLAVE ON *.* to 'user'@'%' identified by '654321';

# 查看容器状态
show master status

+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000003 |     1028 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
```

在从库上进行配置

```
change master to
master_host='172.17.0.2', # 连接的master IP地址
master_user='user',
master_log_file='mysql-bin.000003',
master_log_pos=1028,
master_port=3306,
master_password='654321';

# 执行命令并查看连接状态
mysql > start slave;
mysql > show slave status\G;

*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 172.17.0.2
                  Master_User: user
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000003
          Read_Master_Log_Pos: 1028
               Relay_Log_File: b69cb0f6d536-relay-bin.000002
                Relay_Log_Pos: 503
        Relay_Master_Log_File: mysql-bin.000003
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 1028

...

# 如果配置错误执行stop slave后重新配置
mysql > stop slave;
mysql > 配置...
mysql > start slave;
```

`Slave_IO_Running`和`Slave_SQL_Running`必须是 Yes, 如果是 No 和 Connecting 表示连接失败

失败原因可能有:

1.  连接 master 的 IP 地址有误
2.  配置密码错误
3.  mysql_log_pos 有误

### 测试

在主库创建一个数据库, 在从库上查询

```
# 主库
create database abc default character set utf8;

# 从库
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| abc                |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```

### 参考地址

- https://laravel-china.org/articles/6631/using-docker-to-complete-master-slave-configuration-of-mysql-database
- https://www.jianshu.com/p/0439206e1f28
