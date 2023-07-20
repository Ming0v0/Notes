# 1. MySQL8的主要目录结构

```shell
find / -name mysql
```

##  1.1 数据库文件的存放路径 
MySQL数据库文件的存放路径：`/var/lib/mysql`

MySQL服务器程序在启动时会到文件系统的某一个目录下加载一些文件，之后在运行过程中产生的数据也都会存储到这个目录下的某些文件中，这个目录就称为`数据目录`

MySQL把数据都存到哪个路径下呢？其实`数据目录`对应着一个系统变量`datadir`，我们在使用客户端与服务器建立连接之后查看这个系统变量的值就可以了
```mysql
show variables like 'datadir'; 
+---------------+-----------------+
| Variable_name | Value           |
+---------------+-----------------+
| datadir       | /var/lib/mysql/ |
+---------------+-----------------+
1 row in set (0.00 sec)
```

## 1.2 相关命令目录

相关命令目录：/usr/bin 和/usr/sbin。
![](attachment/Pasted%20image%2020230319135509.png)

安装目录下非常重要的 `bin` 目录，它里边存储了许多关于控制客户端程序和服务器程序的命令 (许多可执行文件，比如`mysql`，`mysqld`，`mysqld_safe` 等)。而`数据目录`是用来存储MySOL在运行过程中产生的数据，注意区分开二者。

## 1.3 配置文件目录
![](attachment/Pasted%20image%2020230319135601.png)
配置文件目录：/usr/share/mysql-8.0（命令及配置文件），/etc/mysql（如my.cnf）

# 2. 数据库和文件系统的关系
像 `InnoDB`、`MyISAM` 这样的存储引擎都是把表存储在磁盘上的，操作系统用来管理磁盘的结构被称为 `文件系统`，所以用专业一点的话来表述就是: 像 `InnoDB` 、 `MyISAM` 这样的存储引擎都是把 `表存储在文件系统上`的。当我们想读取数据的时候，这些存储引擎会从文件系统中把数据读出来返回给我们，当我们想写入数据的时候，这些存储引擎会把这些数据又写回文件系统。本章学习一下`InnoDB` 和`MyISAM` 这两个存储引的数据如何在文件系统中存储。

## 2.1 查看默认数据库
查看一下在我的计算机上当前有哪些数据库
```mysql
mysql> SHOW DATABASES;
```
可以看到有4个数据库是属于MySQL自带的系统数据库
- `mysql`
MySQL 系统自带的核心数据库，它存储了MySOL的用户账户和权限信息，一些存储过程、事件的定义信息，一些运行过程中产生的日志信息，一些帮助信息以及时区信息等。
	
- `information_schema`
MySQL 系统自带的数据库，这个数据库保存着MySOL服务器 `维护的所有其他数据库的信息`，比如有哪些表、哪些视图、哪些触发器、哪些列、哪些索引。这些信息并不是真实的用户数据，而是一些描述性信息，有时候也称之为`元数据`。在系统数据库`information_schema `中提供了一些以`innodb_sys` 开头的表，用于表示内部系统表。
	
- `performance_schema`
MySOL 系统自带的数据库，这个数据库里主要保存MySOL服务器运行过程中的一些状态信息，可以用来 `监控MySQL 服务的各类性能指标`。包括统计最近执行了哪些语句，在执行过程的每个阶段都花费了多长时间，内存的使用情况等信息。
	
- `sys`
MySQL系统自带的数据库，这个数据库主要是通过`视图`的形式把 `information_schema` 和`performance_schema` 结合起来，帮助系统管理员和开发人员监控 MySOL 的技术性能

## 2.2 数据库在文件系统中的表示
看一下我的计算机上的数据目录下的内容：
```shell
sh-4.4# cd /var/lib/mysql
sh-4.4# ls -l
sh-4.4# ls -l
total 96760
-rw-r----- 1 mysql mysql   196608 Mar 19 06:05 '#ib_16384_0.dblwr'
-rw-r----- 1 mysql mysql  8585216 Jan 13 04:46 '#ib_16384_1.dblwr'
drwxr-x--- 2 mysql mysql     4096 Mar 19 05:00 '#innodb_redo'
drwxr-x--- 2 mysql mysql     4096 Mar 19 05:00 '#innodb_temp'
-rw-r----- 1 mysql mysql       56 Jan 13 04:46  auto.cnf
-rw-r----- 1 mysql mysql     9994 Feb 23 12:45  binlog.000013
-rw-r----- 1 mysql mysql      587 Feb 23 14:35  binlog.000014
-rw-r----- 1 mysql mysql      366 Mar 11 16:46  binlog.000015
-rw-r----- 1 mysql mysql      180 Mar 11 17:10  binlog.000016
-rw-r----- 1 mysql mysql     1381 Mar 12 13:00  binlog.000017
-rw-r----- 1 mysql mysql      180 Mar 17 14:00  binlog.000018
-rw-r----- 1 mysql mysql      180 Mar 18 11:54  binlog.000019
-rw-r----- 1 mysql mysql      180 Mar 19 05:00  binlog.000020
-rw-r----- 1 mysql mysql      368 Mar 19 06:05  binlog.000021
-rw-r----- 1 mysql mysql      144 Mar 19 05:00  binlog.index
-rw------- 1 mysql mysql     1676 Jan 13 04:46  ca-key.pem
-rw-r--r-- 1 mysql mysql     1112 Jan 13 04:46  ca.pem
-rw-r--r-- 1 mysql mysql     1112 Jan 13 04:46  client-cert.pem
-rw------- 1 mysql mysql     1680 Jan 13 04:46  client-key.pem
-rw-r----- 1 mysql mysql     4044 Mar 19 05:00  ib_buffer_pool
-rw-r----- 1 mysql mysql 12582912 Mar 19 06:05  ibdata1
-rw-r----- 1 mysql mysql 12582912 Mar 19 05:00  ibtmp1
drwxr-x--- 2 mysql mysql     4096 Jan 13 04:46  mysql
-rw-r----- 1 mysql mysql 31457280 Mar 19 06:05  mysql.ibd
lrwxrwxrwx 1 mysql mysql       27 Mar 19 05:00  mysql.sock -> /var/run/mysqld/mysqld.sock
drwxr-x--- 2 mysql mysql     4096 Jan 13 04:46  performance_schema
-rw------- 1 mysql mysql     1676 Jan 13 04:46  private_key.pem
-rw-r--r-- 1 mysql mysql      452 Jan 13 04:46  public_key.pem
-rw-r--r-- 1 mysql mysql     1112 Jan 13 04:46  server-cert.pem
-rw------- 1 mysql mysql     1680 Jan 13 04:46  server-key.pem
drwxr-x--- 2 mysql mysql     4096 Jan 13 04:46  sys
drwxr-x--- 2 mysql mysql     4096 Mar 19 06:05  temp
-rw-r----- 1 mysql mysql 16777216 Mar 19 06:05  undo_001
-rw-r----- 1 mysql mysql 16777216 Mar 19 05:18  undo_002
```

这个数据目录下的文件和子目录比较多，除了 information_schema 这个系统数据库外，其他的数据库 在 数据目录 下都有对应的子目录。

以我的 `temp` 数据库为例，在MySQL5.7 中打开：
```shell
sh-4.4# cd ./temp 
sh-4.4# ls -l 
总用量 1144 
-rw-r-----. 1 mysql mysql 8658 8月 18 11:32 countries.frm 
-rw-r-----. 1 mysql mysql 114688 8月 18 11:32 countries.ibd 
-rw-r-----. 1 mysql mysql 61 8月 18 11:32 db.opt 
-rw-r-----. 1 mysql mysql 8716 8月 18 11:32 departments.frm 
-rw-r-----. 1 mysql mysql 147456 8月 18 11:32 departments.ibd 
-rw-r-----. 1 mysql mysql 3017 8月 18 11:32 emp_details_view.frm 
-rw-r-----. 1 mysql mysql 8982 8月 18 11:32 employees.frm 
-rw-r-----. 1 mysql mysql 180224 8月 18 11:32 employees.ibd 
-rw-r-----. 1 mysql mysql 8660 8月 18 11:32 job_grades.frm 
-rw-r-----. 1 mysql mysql 98304 8月 18 11:32 job_grades.ibd 
-rw-r-----. 1 mysql mysql 8736 8月 18 11:32 job_history.frm 
-rw-r-----. 1 mysql mysql 147456 8月 18 11:32 job_history.ibd 
-rw-r-----. 1 mysql mysql 8688 8月 18 11:32 jobs.frm 
-rw-r-----. 1 mysql mysql 114688 8月 18 11:32 jobs.ibd 
-rw-r-----. 1 mysql mysql 8790 8月 18 11:32 locations.frm 
-rw-r-----. 1 mysql mysql 131072 8月 18 11:32 locations.ibd 
-rw-r-----. 1 mysql mysql 8614 8月 18 11:32 regions.frm 
-rw-r-----. 1 mysql mysql 114688 8月 18 11:32 regions.ibd
```

在MySQL8.0中打开：
```shell
sh-4.4# cd ./temp 
sh-4.4# ls -l 
总用量 1080 
-rw-r-----. 1 mysql mysql 131072 7月 29 23:10 countries.ibd 
-rw-r-----. 1 mysql mysql 163840 7月 29 23:10 departments.ibd 
-rw-r-----. 1 mysql mysql 196608 7月 29 23:10 employees.ibd 
-rw-r-----. 1 mysql mysql 114688 7月 29 23:10 job_grades.ibd 
-rw-r-----. 1 mysql mysql 163840 7月 29 23:10 job_history.ibd 
-rw-r-----. 1 mysql mysql 131072 7月 29 23:10 jobs.ibd 
-rw-r-----. 1 mysql mysql 147456 7月 29 23:10 locations.ibd 
-rw-r-----. 1 mysql mysql 131072 7月 29 23:10 regions.ibd
```

## 2.3 表在文件系统中的表示

### 2.3.1 InnoDB存储引擎模式

**1.** **表结构**
为了保存表结构，`InnoDB`在`数据目录`下对应的数据库子目录下创建了一个专门用于`描述表结构的文件`
文件名是这样：
```mysql
表名.frm
```
比方说我们在 temp 数据库下创建一个名为 test 的表：
```mysql
mysql> USE temp; 
Database changed 

mysql> CREATE TABLE test (
	-> c1 INT 
	-> ); 
Query OK, 0 rows affected (0.03 sec)
```

那在数据库 `temp` 对应的子目录下就会创建一个名为 `test.frm` 的用于描述表结构的文件。.frm文件 的格式在不同的平台上都是相同的。这个后缀名为.frm是以 `二进制格式` 存储的，我们直接打开是乱码 的。

**2.** **表中数据和索引**

**① 系统表空间（system tablespace）**

默认情况下，InnoDB会在数据目录下创建一个名为`ibdata1`、大小为`12M`的`自拓展`文件，这个文件就是对应的`系统表空间`在文件系统上的表示。怎么才12M？注意这个文件是 自扩展文件 ，当不够用的时候它会自 己增加文件大小。 

当然，如果你想让系统表空间对应文件系统上多个实际文件，或者仅仅觉得原来的 ibdata1 这个文件名 难听，那可以在MySQL启动时配置对应的文件路径以及它们的大小，比如我们这样修改一下my.cnf 配置 文件：

```mysql
[server] 
innodb_data_file_path=data1:512M;data2:512M:autoextend
```

**② 独立表空间(file-per-table tablespace)** 

在MySQL5.6.6以及之后的版本中，InnoDB并不会默认的把各个表的数据存储到系统表空间中，而是为`每一个表建立一个独立表空间`，也就是说我们创建了多少个表，就有多少个独立表空间。使用`独立表空间`来存储表数据的话，会在该表所属数据库对应的子目录下创建一个表示该独立表空间的文件，文件名和表名相同。，只不过添加了一个 `.ibd` 的扩展名而已，所以完整的文件名称长这样：

```mysql
表名.ibd
```

比如：我们使用了 独立表空间 去存储 `temp` 数据库下的 `test` 表的话，那么在该表所在数据库对应 的 `temp` 目录下会为 `test` 表创建这两个文件：

```mysql
test.frm
test.ibd
```
其中 test.ibd 文件就用来存储 test 表中的数据和索引。

> MySQL8.0中不再单独提供`表名.frm`，而是合并在`表名.ibd`文件中。

**③ 系统表空间与独立表空间的设置**

我们可以自己指定使用`系统表空间`还是`独立表空间`来存储数据，这个功能由启动参数`innodb_file_per_table`控制，比如说我们想刻意将表数据都存储到 系统表空间 时，可以在启动 MySQL服务器的时候这样配置：

```ini
[server] 
innodb_file_per_table=0 # 0：代表使用系统表空间； 1：代表使用独立表空间
```

默认情况：
```mysql
mysql> show variables like 'innodb_file_per_table';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| innodb_file_per_table | ON    |
+-----------------------+-------+
1 row in set (0.00 sec)
```

**④ 其他类型的表空间**

随着MySQL的发展，除了上述两种老牌表空间之外，现在还新提出了一些不同类型的表空间，比如通用表空间（general tablespace）、临时表空间（temporary tablespace）等。

### 2.3.2 MyISAM存储引擎模式

**1.** **表结构**

在存储表结构方面， `MyISAM` 和 `InnoDB` 一样，也是在`数据目录`下对应的数据库子目录下创建了一个专门用于描述表结构的文件

```mysql
表名.frm
```

**2.** **表中数据和索引**

在MyISAM中的索引全部都是`二级索引`，该存储引擎的`数据和索引是分开存放`的。所以在文件系统中也是使用不同的文件来存储数据文件和索引文件，同时表数据都存放在对应的数据库子目录下。假如 test 表使用MyISAM存储引擎的话，那么在它所在数据库对应的 `temp` 目录下会为 `test` 表创建这三个文件：

```mysql
test.frm 存储表结构 
test.MYD 存储数据 (MYData) 
test.MYI 存储索引 (MYIndex
```

举例：创建一个 `MyISAM` 表，使用 `ENGINE` 选项显式指定引擎。因为 `InnoDB` 是默认引擎。
```mysql
CREATE TABLE `student_myisam` (
	`id` BIGINT NOT NULL AUTO_INCREMENT,
	`name` VARCHAR ( 64 ) DEFAULT NULL,
	`age` INT DEFAULT NULL,
	`sex` VARCHAR ( 2 ) DEFAULT NULL,
PRIMARY KEY ( `id` ) 
) ENGINE = MYISAM AUTO_INCREMENT = 0 DEFAULT CHARSET = utf8mb3;
```

## 2.4 小结
举例： `数据库a` ， `表b` 。
1、如果表b采用 `InnoDB` ，`data\a`中会产生1个或者2个文件： 
- `b.frm` ：描述表结构文件，字段长度等 
- 如果采用 `系统表空间` 模式的，数据信息和索引信息都存储在 `ibdata1` 中 
- 如果采用 `独立表空间` 存储模式，`data\a`中还会产生 `b.ibd` 文件（存储数据信息和索引信息）

此外： 
① MySQL5.7 中会在data/a的目录下生成 `db.opt` 文件用于保存数据库的相关配置。比如：字符集、比较 规则。而MySQL8.0不再提供db.opt文件。 
② MySQL8.0中不再单独提供b.frm，而是合并在b.ibd文件中。

2、如果表b采用 `MyISAM` ，`data\a`中会产生3个文件： 
- MySQL5.7 中： b.frm ：描述表结构文件，字段长度等。 
  MySQL8.0 中 b.xxx.sdi ：描述表结构文件，字段长度等 
- b.MYD (MYData)：数据信息文件，存储数据信息(如果采用独立表存储模式) 
- b.MYI (MYIndex)：存放索引信息文件