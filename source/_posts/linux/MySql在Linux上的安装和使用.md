---
title: MySql在Linux上的安装和使用
mathjs: true
date: 2021-06-16 12:08:56
tags:
- linux
- mysql
---

# MySql安装环境说明

- OS: manjaro
- MySql的linux-genric的tar包，[下载地址](https://dev.mysql.com/downloads/file/?id=505123)
- neovim
- 使用mycli进行mysql管理+phpmyadmin，这两个的安装可以查看archlinux的wiki

# MySql安装

1. 解压Mysql的tar包

```bashshell
tar -xvf  mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz -C /usr/local/
```

2. 修改文件名

```bash
mv mysql-8.0.25-linux-glibc2.12-x86_64/ mysql
```

3. 修改mysql文件的所有者

```bash
sudo chown -R visualvk /usr/local/mysql
```

4. 创建data文件夹

```bash
cd /usr/local/mysql && mkdir data
```

5. 初始化mysql

```bash
cd /usr/local/mysql
./bin/mysqld --user=root --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --initialize
```

**注意：初始化成功后，会生成一个密码，显示在终端上，把这个密码复制下来，后续修改需要用到**

1. 配置my.cfg，这个文件是mysql的配置文件

```bash
sudo nvim /etc/my.cfg
#以下是my.cfg的文件内容
[client]
# Set mysql client socket file location
socket = /usr/local/mysql/mysql.sock

[mysql]
# Set the mysql client default character set
default-character-set = UTF8MB4
socket = /usr/local/mysql/mysql.sock

[mysqld]
innodb_buffer_pool_size = 128M

# mysql installation directory
basedir = /usr/local/mysql

# mysql data storage directory
datadir = /usr/local/mysql/data

# 
port = 3306

# The machine serial number is 1, indicating the master
server_id = 1

# Set mysql server socket file location
socket = /usr/local/mysql/mysql.sock

# Set the maximum number of mysql connections
max_connections = 20

# The character set used by the server defaults to the 8-bit encoded latin1 character set.
character-set-server = UTF8MB4

# Default storage engine to be used when creating a new table
default-storage-engine = INNODB

# The size of the buffer that can be used by the joint query operation is the same as the sort_buffer_size. The allocated memory corresponding to this parameter is also exclusive for each connection.
join_buffer_size = 128M

# MySQL performs the buffer size used for sorting. If you want to increase the speed of ORDER BY, first look at whether you can let MySQL use the index instead of the extra sorting stage.
sort_buffer_size = 8M

# MySQL random read buffer size. When rows are read in any order (for example, in sort order), a random read buffer is allocated.
read_rnd_buffer_size = 4M 

# A transaction, when not submitted, the generated log is recorded in the Cache; when the transaction commits need to be submitted, the log is persisted to disk.
# default binlog_cache_size size 32K
binlog_cache_size = 1M

# This value (default 8) means that the number of threads stored in the cache can be reused. If there is space in the cache when disconnected, the client thread will be put into the cache.
# If the thread is requested again, the request will be read from the cache. If the cache is empty or a new request, then the thread will be recreated. If there are many new threads,
# Increase this value to improve system performance. You can see the effect of this variable by comparing the variables in the Connections and Threads_created states. (–> indicates the value to be adjusted)
# According to the physical memory setting rules are as follows:
# 1G  —> 8
# 2G  —> 16
# 3G  —> 32
# Greater than 3G —> 64
thread_cache_size = 8

# MySQL query buffer size (starting with 4.0.1, MySQL provides a query buffer mechanism) using query buffer, MySQL stores the SELECT statement and query results in a buffer.
# In the future, for the same SELECT statement (case sensitive), the result will be read directly from the buffer. According to the MySQL user manual, using query buffers can achieve up to 238% efficiency.
# By checking the status value 'Qcache_%', you can know whether the query_cache_size setting is reasonable: If the value of Qcache_lowmem_prunes is very large, it indicates that the buffering is not enough.
# If the value of Qcache_hits is also very large, it means that the query buffer is used very frequently. In this case, you need to increase the buffer size. If the value of Qcache_hits is not large, it means that your query repetition rate is very low.
# In this case, using query buffer will affect efficiency, so you can consider not using query buffer. In addition, adding SQL_NO_CACHE to the SELECT statement can explicitly indicate that the query buffer is not used.
# query_cache_size = 8M

# Specify the buffer size that a single query can use, default 1M
# query_cache_limit = 2M

# Specify the buffer size for the index, increase it to get a better deal of the index (for all reads and multiple rewrites), as much as you can afford. If you make it too big,
# The system will start to change pages and it really slows down. For servers with 4GB or so memory, this parameter can be set to 384M or 512M. By checking the status values Key_read_requests and Key_reads,
# You can know if the key_buffer_size setting is reasonable. The ratio key_reads/key_read_requests should be as lw as possible,
# at least 1:100, 1:1000 is better (the above status values can be obtained using SHOW STATUS LIKE 'key_read%'). Note: The parameter value is set too large, but the overall efficiency of the server is reduced.
key_buffer_size = 4M

#  Minimum length, default 4
ft_min_word_len = 4

# MySQL supports 4 transaction isolation levels, they are:
# READ-UNCOMMITTED, READ-COMMITTED, REPEATABLE-READ, SERIALIZABLE.
# If not specified, MySQL defaults to REPEATABLE-READ, ORACLE defaults to READ-COMMITTED
transaction_isolation = REPEATABLE-READ

# default time zone
default-time_zone = '+8:00'

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO
```

7. 添加mysql的环境变量

```bash
sudo nvim /etc/profile
#在export PATH后面一行加入以下内容
export PATH=$PATH:/usr/local/mysql/bin
```

8. 启动mysql.server

```bash
cd /usr/local/mysql/support-files
./mysql.server start
```

8. 登陆mysql

```bash
mysql -u root -p
# 输入密码后进入mysql,进行修改密码
alter user 'root'@'localhost' identified with native_mysql_password by 'root';
flush privileges;
```

至此，安装完成

# Mysql管理

- 使用mycli进行管理。

常用连接命令

```shell
mycli -u root -host localhost
```

- 使用phpmyadmin管理

这个就是可视化管理

# 安装中的一些问题&解决

- error while loading shared libraries: libnuma.so.1

安装numactl软件包就可以解决

```bash
sudo pacman -S numactl
```

- 没有libtinfo.so.5

根据libtinfo.so.6,创建一个libtinfo.so.5的软链接

```bash
ln -s /usr/lib/libtinfo.so.6 /usr/lib/libtinfo.so.5
```







