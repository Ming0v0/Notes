
**原理示意图**

![](attachment/Pasted%20image%2020230912200649.png)
crontab 进行定时任务的设置，

# 概述

任务调度：是指系统在某个时间执行的特定的命令或程序。

任务调度分类
1. 系统工作：有些重要的工作必须周而复始地执行。如病毒扫描等
2. 个别用户工作：个别用户可能希望执行某些程序，比如对 MySQL 数据库的备份。

**基础语法**
```txt
crontab [选项]
```

常用选项
![](attachment/Pasted%20image%2020230912214638.png)

# 快速入门

**任务的要求**
设置任务调度文件：`/etc/crontab`
设置个人任务调度。执行 `crontab –e` 命令。接着输入任务到调度文件

如：`*/1 * * * * ls  –l   /etc/ > /tmp/to.txt`

意思说每小时的每分钟执行 `ls –l /etc/ > /tmp/to.tx`t 命令

**步骤如下**
1. cron -e
2. `*/ 1 * * * * ls -l /etc >> /tmp/to.txt`
3. 当保存退出后就程开始。
4. 在每一分钟都会自动的调用 `ls -l /etc >> /tmp/to.txt`

**参数细节说明**

![](attachment/Pasted%20image%2020230912215015.png)

![](attachment/Pasted%20image%2020230912215019.png)

# 任务调度的几个演示

案例 1：每隔 1 分钟，就将当前的日期信息，追加到 /tmp/mydate 文件中

先编写一个文件  
```txt
touch /home/mytask1.sh
date >> /tmp/mydate
```

给 mytask1.sh  一个可以执行权限
```txt
chmod 744 /home/mytask1.sh
```

设置个人调度
```txt
crontab -e
*/1 * * * *    /home/mytask1.sh
```

成功

---

案例 2：每隔 1 分钟， 将当前日期和日历都追加到 /home/mycal 文件中

先编写一个文件
```txt
touch /home/mytask2.sh

文件内容
date >> /tmp/mycal 
cal >> /tmp/mycal

```

给 mytask1.sh 一个可以执行权限
```txt
chomd 744 /home/mytask2.sh
```

成功

---

案例 3:  每天凌晨 2:00 将mysql 数据库 testdb  ，备份到文件中mydb.bak

先编写一个文件  
```touch
/home/mytask3.sh

文件内容
/usr/local/mysql/bin/mysqldump -u root -proot testdb > /tmp/mydb.bak
```

给 mytask3.sh 一个可以执行权限
```txt
chmod 744 /home/mytask3.sh
```

设置个人调度
```txt
crontab -e
0 2 * * *    /home/mytask3.sh
```

成功

# crond 相关指令
- conrtab –r：终止任务调度。
- crontab –l：列出当前有那些任务调度
- service crond restart  重启任务调度
