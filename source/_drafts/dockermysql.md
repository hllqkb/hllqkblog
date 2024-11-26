---
abbrlink: ''
categories: []
cover: https://i.hllqk.cn/file/66cf49c4dfb1ecab775f5-be1f1781867b49cbda.png
date: '2024-11-26T16:09:47.256212+08:00'
tags: []
title: windows docker里安装并使用mysql（内含mysql主从搭建）
updated: '2024-11-26T16:28:05.118+08:00'
---
安装请看这里：[docker介绍和安装以及常用命令](https://blog.csdn.net/u012643122/article/details/125904267)，现在假设已经安装好了**docker**。

## 第一步，命令行拉取**mysql**镜像

```bash
docker pull mysql:latest
```

查看是否拉取成功

```bash
docker images mysql:latest
```

## 第二步，运行mysql镜像，启动mysql实例

```bash
docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD="123456" -d mysql:latest
```

`3306:3306`前面的是mysql在**windows**里端口，后面的是mysql在docker里端口，如果windows外面安装了mysql，端口也是3306，那么要先关闭windows的mysql，不然会有端口冲突：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2cd66d41812153fe2233e3a1819bc70c.png)

查看mysql是否启动

```bash
docker ps
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ed00872e7d14109727f132107f6d649d.png)

## 第三步，进入容器实例，登录mysql

进入容器实例：

```bash
docker exec -it 3bac8e05792e /bin/bash
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a4b3809169ae64a3dd2c34cf8f9e8bc4.png)
退出bash命令行：

```bash
exit
```

复制和粘贴命令行上的文本：
选中文本，按一下鼠标右键即可复制，在按一下鼠标右键即可粘贴。

然后输入`mysql -uroot -p`进行登录：

```bash
mysql -uroot -p
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/17d1c250d94e75c5aca8a2349d40b6a9.png)
输入密码后进入mysql控制台：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6ac9165221b4106c2d5961a1abe20736.png)
查询字符集：

```sql
show variables like 'character%';
```

退出[mysql命令行](https://so.csdn.net/so/search?q=mysql%E5%91%BD%E4%BB%A4%E8%A1%8C&spm=1001.2101.3001.7020)：

```bash
exit
```

## 第四步，在windows上使用mysql客户端工具进行连接

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b8cac9ca8b71d0246bd0091694b646ba.png)

## 第五步，解决数据备份

为了解决docker里的mysql容器删除后数据丢失问题，需要修改容器创建的代码：
宿主机是**linux**

```bash
docker run --restart=always -d -p 3306:3306 --privileged=true -v /mydata/mysql/log:/var/log/mysql -v /mydata/mysql/data:/var/lib/mysql -v /mydata/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD="123456" --name mysql mysql:latest
```

宿主机是windows

```bash
docker run --restart=always -d -p 3306:3306 --privileged=true -v "C:/Users/tang8/.docker/mydata/mysql/log":"/var/log/mysql" -v "C:/Users/tang8/.docker/mydata/mysql/data":"/var/lib/mysql" -v "C:/Users/tang8/.docker/mydata/mysql/conf":"/etc/mysql/conf.d" -e MYSQL_ROOT_PASSWORD="123456" --name mysql mysql:latest
```

**这里注意**：有些docker的mysql镜像的`my.cnf文件`和`my.cnf.d目录`配置文件在`/etc`目录下，不在`/etc/mysql`目录下，对应命令需要修改成：
先在宿主机新建文件`/mydata/mysql/conf/my.cnf`，文件内容如下：

```bash
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html
[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
```

然后创建mysql容器：

```bash
docker run --restart=always -d -p 3306:3306 --privileged=true -v /mydata/mysql/log:/var/log/mysql -v /mydata/mysql/data:/var/lib/mysql -v /mydata/mysql/conf/my.cnf:/etc/my.cnf -v /mydata/mysql/conf/my.cnf.d:/etc/my.cnf.d -e MYSQL_ROOT_PASSWORD="123456" --name mysql mysql:8.0
```

`友情提示：my.cnf文件内容和路径如何获取？可以创建一个不挂载任何数据卷的mysql容器（docker run -d -p 3306:3306 --name mysql mysql:8.0），然后docker exec命令进入后查看my.cnf到底在哪个路径，并使用docker cp命令将my.cnf文件拷贝到宿主机。`

其中，`--privileged=true`就是让容器拥有root权限，`-v /mydata/mysql/log:/var/log/mysql`是将数据库日志从`/var/log/mysql`备份到`/mydata/mysql/log`容器数据卷中。

privileged详细介绍：
privileged值为true时，container内的root拥有真正的root权限。privileged为true启动的容器可以看到很多host上的设备，可以执行mount，可以在docker容器中启动docker容器。
privileged值为false时，container内的root只是外部的一个普通用户权限，默认为false。

如何删除数据卷：
删除容器时将数据卷一并删除，如果还有别的容器引用该数据卷，则删除失败。

```bash
docker rm -v 容器ID|数据卷名
```

只删除数据卷，如果还有容器引用该数据卷，则删除失败。

```bash
docker volume rm 数据卷名
```

删除所有未被使用的数据卷，不指定名称。

```bash
docker volume prune
```

## 第六步，解决乱码问题

为了解决docker里的mysql无法存储中文问题，我百度了一番，发现有人提供的解决方案是需要修改容器创建的代码：

```bash
docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD="123456" -d mysql:latest --character-set-connection=utf8 --character-set-client=utf8
```

如果mysql容器处于Exited状态，docker ps命令查不出来，那么可以使用：

```bash
docker logs --tail 200 容器ID
```

查看错误日志，200是指定只看200行。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3d385829dd247c5c1640dea2abd9c29c.png)
我发现以上方式行不通，mysql根本不认`unknown variable 'character-set-connection=utf8'`，还得采用原来的老办法。容器里的mysql和外面的mysql并没有本质区别，其配置文件在`/etc/mysql/conf.d`，也可以再添加自己的配置文件`/etc/mysql/my.cnf`。

老办法如下：

[https://blog.csdn.net/u012643122/article/details/46799943](https://blog.csdn.net/u012643122/article/details/46799943)

[https://blog.csdn.net/u012643122/article/details/47980233](https://blog.csdn.net/u012643122/article/details/47980233)

## 第七步，设置mysql主从数据库

### 新建主服务器容器实例3307

```bash
docker run -d -p 3307:3306 -v "C:/Users/tang8/.docker/mydata/mysql-master/log":"/var/log/mysql" -v "C:/Users/tang8/.docker/mydata/mysql-master/data":"/var/lib/mysql" -v "C:/Users/tang8/.docker/mydata/mysql-master/conf":"/etc/mysql/conf.d" -e MYSQL_ROOT_PASSWORD="123456" --name mysql-master mysql:latest
```

### 进入`C:/Users/tang8/.docker/mydata/mysql-master/conf`目录下新建my.cnf

文件内容如下：

```bash
[client]
default-character-set=utf8

[mysqld]
#服务ID，同一局域网内需要唯一
server_id=1000
#无需同步的数据库名称
binlog-ignore-db=mysql
#开启二进制日志功能
log-bin=mall-mysql-bin
#设置使用的二进制日志格式（mixed，statement，row）
binlog_format=mixed
#设置二进制日志过期清理时间，默认值为0,，表示不自动清理
expire_logs_days=7
#跳过主从复制中遇到的所有错误或者指定类型的错误，避免slave端的复制中断
#如1062错误是指一些主键重复，1032错误是指主从数据库的数据不一致。
slave_skip_errors=1062

character-set-server=utf8
init_connect='SET NAMES utf8'

[mysqld_safe]
default-character-set=utf8

[mysql]
default-character-set=utf8
```

### 修改完配置后重启容器实例3307

```bash
docker restart mysql-master
```

### 降低3307实例的/etc/mysql/conf.d/my.cnf文件权限

重启容器后`/etc/mysql/conf.d/`目录下会出现我们刚刚在windows上新增my.cnf文件，不过此时文件权限太高需要减低，不然mysql会拒绝读取。

进入实例

```bash
docker exec -it mysql-master /bin/bash
```

修改权限

```bash
chmod 644 /etc/mysql/conf.d/my.cnf
```

再次重启

```bash
docker restart mysql-master
```

my.cnf权限相关文章：[https://blog.csdn.net/u012643122/article/details/107280186](https://blog.csdn.net/u012643122/article/details/107280186)

### 在容器实例3307内创建数据同步用户

进入实例

```bash
docker exec -it mysql-master /bin/bash
```

登录mysql：

```bash
mysql -uroot -p
```

```sql
/*创建slave用户，不绑定IP（@'%'），密码为123456*/
create user 'slave'@'%' identified by '123456';
/*将replication slave权限和replication client权限授予slave用户，不限IP（@'%'），不限库（on *.*），不限表（on *.*）*/
grant replication slave,replication client on *.* to 'slave'@'%';
```

主机上的数据只能由指定用户来同步，避免数据泄露。

权限相关文章：[https://blog.csdn.net/u012643122/article/details/52643715](https://blog.csdn.net/u012643122/article/details/52643715)

### 新建从服务器容器实例3308

```bash
docker run -d -p 3308:3306 -v "C:/Users/tang8/.docker/mydata/mysql-slave/log":"/var/log/mysql" -v "C:/Users/tang8/.docker/mydata/mysql-slave/data":"/var/lib/mysql" -v "C:/Users/tang8/.docker/mydata/mysql-slave/conf":"/etc/mysql/conf.d" -e MYSQL_ROOT_PASSWORD="123456" --name mysql-slave mysql:latest
```

### 进入`C:/Users/tang8/.docker/mydata/mysql-slave/conf`目录下新建my.cnf

文件内容如下：

```bash
[client]
default-character-set=utf8

[mysqld]
#服务ID，同一局域网内需要唯一
server_id=1001
#无需同步的数据库名称
binlog-ignore-db=mysql
#开启二进制日志功能
log-bin=mall-mysql-slave1-bin
#设置使用的二进制日志格式（mixed，statement，row）
binlog_format=mixed
#设置二进制日志过期清理时间，默认值为0,，表示不自动清理
expire_logs_days=7
#跳过主从复制中遇到的所有错误或者指定类型的错误，避免slave端的复制中断
#如1062错误是指一些主键重复，1032错误是指主从数据库的数据不一致。
slave_skip_errors=1062
#relay_log配置中继日志
relay_log=mall-mysql-relay-bin
#将复制事件写入自己的二进制日志
log_slave_updates=1
#设置数据库数据为只读（具有super权限的用户除外）
read_only=1


character-set-server=utf8
init_connect='SET NAMES utf8'

[mysqld_safe]
default-character-set=utf8

[mysql]
default-character-set=utf8
```

### 修改完配置后重启容器实例3308

```bash
docker restart mysql-slave
```

### 降低3308实例的/etc/mysql/conf.d/my.cnf文件权限

重启容器后`/etc/mysql/conf.d/`目录下会出现我们刚刚在windows上新增my.cnf文件，不过此时文件权限太高需要减低，不然mysql会拒绝读取。

进入实例

```bash
docker exec -it mysql-slave /bin/bash
```

修改权限

```bash
chmod 644 /etc/mysql/conf.d/my.cnf
```

再次重启

```bash
docker restart mysql-slave
```

my.cnf权限相关文章：[https://blog.csdn.net/u012643122/article/details/107280186](https://blog.csdn.net/u012643122/article/details/107280186)

### 在主数据库3307中查看主从同步状态

1，进入实例mysql-master，省略
2，登录mysql，省略
3，`show master status;`
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9d2d68f76d16b3ff00eab1c7868a2874.png)

### 在从数据库3308中配置主数据库相关信息

1，进入实例mysql-slave，省略
2，登录mysql，省略
3，认主归从

```sql
change master to master_host='172.17.0.2',master_user='slave',master_password='123456',master_port=3306,master_log_file='mall-mysql-bin.000001',master_log_pos=157,master_connect_retry=30;
```

或者

```sql
change master to master_host='192.168.1.217',master_user='slave',master_password='123456',master_port=3307,master_log_file='mall-mysql-bin.000001',master_log_pos=157,master_connect_retry=30;
```

记住这个master\_host一定不可能是127.0.0.1，而是master容器的ip地址。

#### 如何查询master容器的IP？

1，进入实例mysql-master，省略
2，`cat /etc/hosts`
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/411da9fe396e587f1b49949a5329c59a.png)
使用容器IP的话，端口就要用3306,。也可以不使用容器的IP，使用外部宿主机器windows的IP也是可以的。不过如果使用宿主机IP的话，端口就要使用3307。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a7ca7c3d3b6f12c51a0c7223884968cf.png)

### master相关关键词释义

master\_host=主数据库IP
master\_port=主数据端口
master\_user=在主数据库创建的用于同步的用户账号
master\_password=在主数据库创建的用于同步的用户密码
master\_log\_file=指定从主数据库复制数据的日志文件，查看主数据状态的File字段的值
master\_log\_pos=指定从主数据库哪个位置开始复制数据，查看主数据状态的Position字段的值
master\_connect\_retry=从数据库连接主数据库失败时，重新尝试连接的间隔时间，单位秒

### 在从数据库3308中查看主从同步状态

1，进入实例mysql-master，省略
2，登录mysql，省略
3，`show slave status \G;`，`\G`只是用键值对格式输出结果，方便查看数据。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/36a3ec3824484438bf510472c51bf2dc.png)
主要看 Slave\_IO\_Running和Slave\_SQL\_Running的值是Yes还是No。

### 在从数据库3308中开启主从同步

在从数据库执行SQL`start slave;`开启主从同步：

```sql
start slave;
```

关闭主从同步：

```sql
stop slave;
```

### 再从数据库3308中再次查看主从同步状态

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9cfccab59ced0d0ee9a72d8649927a1a.png)
如果连不上，Slave\_IO\_Running的值一直是Connecting，那就退出mysql命令行，在bash命令行上，使用`mysql -h IP -P 端口 -u 用户名 -p`命令连接master数据库试试能不能连上。
`mysql -h master_host -P 3307 -u slave -p`

### Authentication plugin 'caching\_sha2\_password’问题

如果使用宿主windows的ip有连接错误，slave连不上master：

```bash
error connecting to master 'slave@192.168.1.217:3307' - retry-time: 30 retries: 3 message: Authentication plugin 'caching_sha2_password' reported error: Authentication requires secure connection.
```

或者使用docker的ip有连接错误，slave连不上master：

```bash
error connecting to master 'slave@172.17.0.4:3306' - retry-time: 30 retries: 1 message: Authentication plugin 'caching_sha2_password' reported error: Authentication requires secure connection.
```

都是因为mysql8.0会默认使用`caching_sha2_password`插件来校验用户slave身份，但是服务器不会将公用密钥发送给客户端，并且客户端未提供公用密钥，因此它无法加密密码并且连接失败。
解决方案一：
要从服务器请求RSA公钥，需要指定选项 --get-**server**-public-key ，也就是说需要在slave请求服务器公钥：

```sql
mysql -u slave -p123456 -h 192.168.1.217 -P3307 --get-server-public-key
```

或者设置好master的密钥路径：

```sql
mysql -u repl -p123 -h 192.168.1.217 -P3307 --server-public-key-path=/mysqldata/slave/key/public_key1.pem
```

在这种情况下，服务器将RSA公钥发送给客户端，后者使用它来加密密码并将结果返回给服务器。插件使用服务器端的RSA私钥解密密码，并根据密码是否正确来接受或拒绝连接。
然后重新在从库配置change masrer to并且start slave，复制可以正常启动：

```sql
stop slave;
reset slave;
change master to master_host='192.168.1.217',master_user='slave',master_password='123456',master_port=3307,master_log_file='mall-mysql-bin.000001',master_log_pos=157,master_connect_retry=30;
start slave;
```

解决方案二：
在master中修改slave用户，使其避免使用插件caching\_sha2\_password。

```sql
create user 'slave'@'%' identified with 'mysql_native_password' by '123456';
grant replication slave,replication client on *.* to 'slave'@'%';
flush privileges;
select user,host,plugin,authentication_string from mysql.user;
```

或者：

```sql
alter user 'slave'@'%' identified with mysql_native_password BY '123456';
flush privileges;
select user,host,plugin,authentication_string from mysql.user;
```

### Slave\_SQL\_Running: No，Last\_Errno: 1146问题

```bash
Slave_IO_Running: Yes
Slave_SQL_Running: No
...
Last_Errno: 1146
Last_Error: Coordinator stopped because there were error(s) in the worker(s).  The most recent failure being: Worker 1 failed executing transaction 'ANONYMOUS' at master log mall-mysql-bin. 000004, end_log_pos 1336.  See error log and/or performance_schema. replication_applier_status_by_worker table for more details about this failure or others, if any.
```

### 添加测试数据测试主从同步是否工作

在主数据库建库建表，查看从数据是否同步更新。
