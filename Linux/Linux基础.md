# crond 任务调度
原理示意图
![](attachment/Pasted%20image%2020230912200649.png)
crontab 进行 定时任务的设置，

## 概述

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

## 快速入门

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

## 任务调度的几个演示
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

## crond 相关指令
- conrtab –r：终止任务调度。
- crontab –l：列出当前有那些任务调度
- service crond restart  重启任务调度

# Linux 磁盘分区、挂载
## 分区基础知识
磁盘分区是将硬盘划分为不同的逻辑部分的过程。每个分区可以被视为一个独立的磁盘，它具有自己的文件系统，并可以存储数据和文件。

**分区的方式**
MBR 分区
1. 最多支持四个主分区
2. 系统只能安装在主分区
3. 扩展分区要占一个主分区
4. MBR 最大只支持 2TB，但拥有最好的兼容性

GTP 分区
1. 支持无限多个主分区（但操作系统可能限制，比如 windows 下最多 128 个分区）
2. 最大支持 18EB 的大容量（1EB=1024 PB，1PB=1024 TB  ）
3. windows7 64 位以后支持 gtp

**windows 下的磁盘分区**
Windows的磁盘分区原理是基于磁盘分区表（Partition Table）来管理磁盘的分区。分区表存储在硬盘的第一个扇区，通常称为主引导记录（Master Boot Record，MBR）或GUID分区表（GUID Partition Table，GPT）。磁盘分区表记录了硬盘上所有分区的位置和大小，使操作系统能够正确地识别和访问这些分区。

在Windows中，常用的分区工具有磁盘管理器（Disk Management）和命令行工具DiskPart。这些工具提供了创建、删除、调整和格式化分区的功能。

Windows支持两种主要的分区类型：主分区和扩展分区。主分区是可以直接被操作系统引导的分区，而扩展分区则可以容纳多个逻辑分区。一个硬盘最多可以有四个主分区或三个主分区加一个扩展分区。

每个分区都可以分配一个驱动器号或挂载点，以便操作系统可以访问和管理分区中的文件和数据。分区可以被格式化为不同的文件系统，如FAT32、NTFS等，不同的文件系统会影响文件存储的方式和限制。

磁盘分区的好处是可以更好地组织数据，提高存储空间的利用率，同时可以方便地对不同的分区进行备份和恢复。此外，磁盘分区还可以提高系统的性能，因为操作系统可以更好地管理和访问不同的分区。

![](attachment/Pasted%20image%2020230912231437.png)

**Linux 分区**
**原理介绍**
Linux 来说无论有几个分区，分给哪一目录使用，它归根结底就只有一个根目录，一个独立且唯一的文件结构 , Linux 中每个分区都是用来组成整个文件系统的一部分。

Linux 采用了一种叫“载入”的处理方法，它的整个文件系统中包含了一整套的文件和目录， 且将一个分区和一个目录联系起来。这时要载入的一个分区将使它的存储空间在一个目录下获得。

示意图
![](attachment/Pasted%20image%2020230912231725.png)


**硬盘说明**
Linux 硬盘分 IDE 硬盘和 SCSI 硬盘，目前基本上是 SCSI 硬盘

对于 IDE 硬盘，驱动器标识符为“hdx~”,其中“hd”表明分区所在设备的类型，这里是指 IDE 硬盘了。“x”为盘号（a 为基本盘，b 为基本从属盘，c 为辅助主盘，d 为辅助从属盘）,“~”代表分区， 前四个分区用数字 1 到 4 表示，它们是主分区或扩展分区，从 5 开始就是逻辑分区。例，hda3 表示为第一个IDE 硬盘上的第三个主分区或扩展分区,hdb2 表示为第二个IDE 硬盘上的第二个主分区或扩展分区。

对于 SCSI 硬盘则标识为“sdx~”，SCSI 硬盘是用“sd”来表示分区所在设备的类型的，其余则和 IDE 硬盘的表示方法一样。

**使用lsblk 指令查看当前系统的分区情况**
![](attachment/Pasted%20image%2020230912232706.png)

![](attachment/Pasted%20image%2020230912232709.png)



## 如何增加一块硬盘

挂载的经典案例
需求是给我们的 Linux 系统增加一个新的硬盘，并且挂载到/home/newdisk

虚拟机添加硬盘

分区    
```txt
fdisk /dev/sdb
```

格式化  
```txt
mkfs -t ext4  /dev/sdb1
```

挂载   
先创建一个被挂载的目录
```txt
/home/newdisk
```
挂载 
```txt
mount /dev/sdb1 /home/newdisk
```

设置可以自动挂载(永久挂载，当你重启系统，仍然可以挂载到 /home/newdisk) 。

```txt
vim  /etc/fstab

文件内容
/dev/sdb1  /home/newdisk   ext4   defaults   0 0
```

**具体的操作步骤整理**

**虚拟机增加硬盘步骤 1**
![](attachment/Pasted%20image%2020230912233835.png)
在【虚拟机】菜单中，选择【设置】，然后设备列表里添加硬盘，然后一路【下一步】，中间只有选择磁盘大小的地方需要修改，至到完成。然后重启系统（才能识别）

---

**虚拟机增加硬盘步骤 2**

分区命令  
```txt
fdisk  /dev/sdb
```

开始对/sdb 分区
- m    显示命令列表
- p     显示磁盘分区 同 fdisk    –l
- n     新增分区
- d     删除分区
- w    写入并退出
说明： 开始分区后输入 n，新增分区，然后选择 p ，分区类型为主分区。两次回车默认剩余全部空间。最后输入 w 写入分区并退出，若不保存退出输入 q。
![](attachment/Pasted%20image%2020230912234009.png)

---

**虚拟机增加硬盘步骤 3**
格式化磁盘
分区命令
```txt
mkfs -t ext4 /dev/sdb1
```
其中 ext4 是分区类型

---

**虚拟机增加硬盘步骤 4**

挂载
将一个分区与一个目录联系起来，

```txt
mount 设备名称 挂载目录
```

例如
```txt
mount /dev/sdb1 /newdisk
```

卸载分区
```txt
umount   设备名称 或者   挂载目录
```

例如  
```txt
umount /dev/sdb1 
umount /newdisk
```

---

**虚拟机增加硬盘步骤 5**

永久挂载:  通过修改`/etc/fstab` 实现挂载添加完成后 执行 `mount –a` 即刻生效

![](attachment/Pasted%20image%2020230912234440.png)

## 磁盘情况查询
### 查询系统整体磁盘使用情况

基本语法
```txt
df -h
```

演示
查询系统整体磁盘使用情况
![](attachment/Pasted%20image%2020230912234917.png)

### 查询指定目录的磁盘占用情况

基本语法
```txt
du -h    /目录
```

查询指定目录的磁盘占用情况，默认为当前目录
- -s 指定目录占用大小汇总
- -h 带计量单位
- -a 含文件
- --max-depth=1    子目录深度
- -c 列出明细的同时，增加汇总值

演示
查询 /opt 目录的磁盘占用情况，深度为 1
![](attachment/Pasted%20image%2020230912235054.png)

### 磁盘情况-工作实用指令
统计/home 文件夹下文件的个数
![](attachment/Pasted%20image%2020230912235319.png)

统计/home 文件夹下目录的个数
![](attachment/Pasted%20image%2020230912235744.png)

统计/home 文件夹下文件的个数，包括子文件夹里的
![](attachment/Pasted%20image%2020230912235758.png)

统计文件夹下目录的个数，包括子文件夹里的
![](attachment/Pasted%20image%2020230912235820.png)

以树状显示目录结构
![](attachment/Pasted%20image%2020230912235841.png)

# 网络配置
## DHCP
![](attachment/Pasted%20image%2020230913145736.png)
缺点: linux 启动后会自动获取 IP,缺点是每次自动获取的 ip 地址可能不一样。这个不适用于做服务器，因为我们的服务器的 ip 需要时固定的。

## 固定的IP

说明
直 接 修 改 配 置 文 件 来 指 定 IP, 并 可 以 连 接 到 外 网 ( 程 序 员 推 荐 ) ， 编 辑                                 
```txt
vi /etc/sysconfig/network-scripts/ifcfg-eth0
```

要求：将 ip 地址配置的静态的，ip 地址为 192.168.184.130
![](attachment/Pasted%20image%2020230913145830.png)

修改后，一定要 重启服务
1. service network restart
2. reboot （重启系统）

![](attachment/Pasted%20image%2020230913145911.png)

# 进程管理

## 进程的基本介绍

在 LINUX 中，每个执行的程序（代码）都称为一个进程。每一个进程都分配一个 ID 号。

每一个进程，都会对应一个父进程，而这个父进程可以复制多个子进程。例如 www 服务器。

每个进程都可能以两种方式存在的。前台与后台，所谓前台进程就是用户目前的屏幕上可以进行操作的。后台进程则是实际在操作，但由于屏幕上无法看到的进程，通常使用后台方式执行。

一般系统的服务都是以后台进程的方式存在，而且都会常驻在系统中。直到关机才才结束。

## 显示系统执行的进程

查看进行使用的指令是 `ps` ,一般来说使用的参数是 `ps -aux`
![](attachment/Pasted%20image%2020230913150224.png)
![](attachment/Pasted%20image%2020230913150245.png)

**ps 指令详解**
指令
```txt
ps –aux|grep xxx 
```
比如我看看有没有 sshd 服务

指令说明

- System V 展示风格
- USER：用户名称
- PID：进程号
- %CPU：进程占用 CPU 的百分比
- %MEM：进程占用物理内存的百分比
- VSZ：进程占用的虚拟内存大小（单位：KB）
- RSS：进程占用的物理内存大小（单位：KB）
- TT：终端名称,缩写 .
- STAT：进程状态，其中 S-睡眠，s-表示该进程是会话的先导进程，N-表示进程拥有比普通优先级更低的优先级，R-正在运行，D-短期等待，Z-僵死进程，T-被跟踪或者被停止等等
- STARTED：进程的启动时间
- TIME：CPU 时间，即进程使用 CPU 的总时间
- COMMAND：启动进程所用的命令和参数，如果过长会被截断显示

**应用实例**
要求：以全格式显示当前所有的进程，查看进程的父进程。
![](attachment/Pasted%20image%2020230913150652.png)

- ps -ef 是以全格式显示当前所有的进程
- -e 显示所有进程。-f 全格式。
- ps -ef | grep xxx
- 是 BSD 风格
- UID：用户 ID
- PID：进程 ID
- PPID：父进程 ID
- C：CPU 用于计算执行优先级的因子。数值越大，表明进程是 CPU 密集型运算，执行优先级会降低；数值越小，表明进程是 I/O 密集型运算，执行优先级会提高
- STIME：进程启动的时间
- TTY：完整的终端名称
- TIME：CPU 时间
- CMD：启动进程所用的命令和参数

思考题，如果我们希望查看 sshd 进程的父进程号是多少，应该怎样查询 ？
![](attachment/Pasted%20image%2020230913150918.png)

## 终止进程kill 和 killall

介绍
进程执行一半需要停止时，或是已消了很大的系统资源时，此时可以考虑停止该进程。使用 kill 命令来完成此项任务。

基本语法
```txt
kill  [选项] 进程号（功能描述：通过进程号杀死进程）
```

```txt
killall 进程名称
（功能描述：通过进程名称杀死进程，也支持通配符，这在系统因负载过大而变得很慢时很有用）
```

常用选项
- -9 :表示强迫进程立即停止

**最佳实践**
案例 1：踢掉某个非法登录用户
![](attachment/Pasted%20image%2020230913151300.png)

案例 2: 终止远程登录服务 sshd, 在适当时候再次重启 sshd 服务
![](attachment/Pasted%20image%2020230913151408.png)

案例 3: 终止多个 gedit  编辑器 【killall ,  通过进程名称来终止进程】
![](attachment/Pasted%20image%2020230913151450.png)

案例 4：强制杀掉一个终端
![](attachment/Pasted%20image%2020230913151502.png)

## 查看进程树 pstree

**基本语法**
```txt
pstree [选项] ,可以更加直观的来看进程信息
```

常用选项
- -p :显示进程的 PID
- -u :显示进程的所属用户

**应用实例**
案例 1：请你树状的形式显示进程的 pid
![](attachment/Pasted%20image%2020230913151749.png)

案例 2：请你树状的形式进程的用户 id 
pstree -u 即可。

## 服务(Service)管理

**介绍**
服务(service) 本质就是进程，但是是运行在后台的，通常都会监听某个端口，等待其它程序的请求，比如(mysql , sshd 防火墙等)，因此我们又称为守护进程.

原理图
![](attachment/Pasted%20image%2020230913152010.png)

service 管理指令
```txt
service    服务名 [start|stop|restart|reload|status]

```
在 CentOS7.0 后 不再使用 service ,而是 systemctl

**使用案例**
查看当前防火墙的状况，关闭防火墙和重启防火墙。
![](attachment/Pasted%20image%2020230913152940.png)

![](attachment/Pasted%20image%2020230913152945.png)

细节讨论
关闭或者启用防火墙后，立即生效。telnet 测试 某个端口即可
![](attachment/Pasted%20image%2020230913153023.png)

**查看服务名**
方式 1：使用 setup -> 系统服务 就可以看到。
![](attachment/Pasted%20image%2020230913153106.png)

方式 2：`/etc/init.d/服务名称`
![](attachment/Pasted%20image%2020230913153139.png)

---

**服务的运行级别(runlevel):**
查看或者修改默认级别：  vi /etc/inittab
Linux 系统有 7 种运行级别(runlevel)：常用的是级别 3 和 5
- 运行级别 0：系统停机状态，系统默认运行级别不能设为 0，否则不能正常启动
- 运行级别 1：单用户工作状态，root 权限，用于系统维护，禁止远程登陆
- 运行级别 2：多用户状态(没有 NFS)，不支持网络
- 运行级别 3：完全的多用户状态(有 NFS)，登陆后进入控制台命令行模式
- 运行级别 4：系统未使用，保留
- 运行级别 5：X11 控制台，登陆后进入图形 GUI 模式
- 运行级别 6：系统正常关闭并重启，默认运行级别不能设为 6，否则不能正常启动

---

**开机的流程说明**
![](attachment/Pasted%20image%2020230913153246.png)

**chkconfig 指令介绍**

通过chkconfig 命令可以给每个服务的各个运行级别设置自启动/关闭基本语法

查看服务
```txt
chkconfig --list | grep xxx
```

![](attachment/Pasted%20image%2020230913153324.png)

![](attachment/Pasted%20image%2020230913153345.png)

查看某个服务
```txt
chkconfig  服务名 --list
```

![](attachment/Pasted%20image%2020230913153333.png)


请将 sshd 服务在运行级别为  5 的情况下，不要自启动。
```txt
chkconfig --level 5 服务名 on/off
```

![](attachment/Pasted%20image%2020230913153422.png)


**应用实例**

案例 1： 请显示当前系统所有服务的各个运行级别的运行状态
```txt
chkconfig --list
```

案例 2 ：请查看 sshd 服务的运行状态
```txt
service sshd status
```

案例 3： 将 sshd 服务在运行级别 5 下设置为不自动启动，看看有什么效果？
```txt
chkconfig --level 5 sshd off
```

案例 4： 当运行级别为 5 时，关闭防火墙。
```txt
chkconfig --level 5 iptables off
```

案例 5： 在所有运行级别下，关闭防火墙
```txt
chkconfig    iptables off
```

案例 6： 在所有运行级别下，开启防火墙
```txxt
chkconfig    iptables    on
```

使用细节
chkconfig 重新设置服务后自启动或关闭，需要重启机器 reboot 才能生效

## 动态监控进程

介绍
top 与 ps 命令很相似。它们都用来显示正在执行的进程。Top 与 ps 最大的不同之处，在于 top **在执行一段时间可以更新正在运行的的进程**。

**基本语法**
```txt
top [选项]
```

选项说明
![](attachment/Pasted%20image%2020230913154038.png)

![](attachment/Pasted%20image%2020230913154042.png)

应用实例
案例 1：监视特定用户
top：输入此命令，按回车键，查看执行的进程。
u：然后输入“u”回车，再输入用户名，即可

![](attachment/Pasted%20image%2020230913154128.png)

案例 2：终止指定的进程。
top：输入此命令，按回车键，查看执行的进程。
k：然后输入“k”回车，再输入要结束的进程 ID 号
![](attachment/Pasted%20image%2020230913154239.png)

案例 3:指定系统状态更新的时间(每隔 10 秒自动更新， 默认是 3 秒)：
```txt
top -d 10
```

## 查看系统网络情况netstat

基本语法
```txt
netstat [选项] netstat -anp
```

选项说明
- -an  按一定顺序排列输出
- -p  显示哪个进程在调用

应用案例
查看系统所有的网络服务
![](attachment/Pasted%20image%2020230913154509.png)

请查看服务名为 sshd 的服务的信息。
![](attachment/Pasted%20image%2020230913154527.png)

# Shell 编程
## Shell 是什么
![](attachment/Pasted%20image%2020230913154948.png)

Shell 是一个命令行解释器，它为用户提供了一个向 Linux 内核发送请求以便运行程序的界面系统级程序，用户可以用 Shell 来启动、挂起、停止甚至是编写一些程序.

## Shell 脚本的执行方式

脚本格式要求
1. 脚本以`#!/bin/bash` 开头
2. 脚本需要有可执行权限

编写第一个Shell 脚本
需求说明
创建一个 Shell 脚本，输出 hello world!

```shell
#!/bin/bash
echo "hello,world"
```

脚本的常用执行方式
方式 1：输入脚本的**绝对路径**或**相对路径**
- 首先要赋予 helloworld.sh  脚本的+x 权限
- 执行脚本
![](attachment/Pasted%20image%2020230913155517.png)

方式 2(sh+脚本)，不推荐
说明：不用赋予脚本+x 权限，直接执行即可
![](attachment/Pasted%20image%2020230913155539.png)

## shell 变量

Linux Shell 中的变量分为，**系统变量**和**用户自定义变量**。
- 系统变量：`$HOME、$PWD、$SHELL、$USER `等等，比如: `echo $HOME` 等等..
![](attachment/Pasted%20image%2020230913155907.png)

- 显示当前 shell 中所有变量：set


 **shell 变量的定义**
基本语法
- 定义变量：`变量=值 `
- 撤销变量：`unset 变量`
- 声明静态变量：`readonly 变量`，注意：不能 `unset`

快速入门
案例 1：定义变量 A
案例 2：撤销变量 A
![](attachment/Pasted%20image%2020230913160053.png)

案例 3：声明静态的变量 B=2，不能 unset
![](attachment/Pasted%20image%2020230913160126.png)

案例 4：可把变量提升为全局环境变量，可供其他 shell 程序使用

**定义变量的规则**
- 变量名称可以由字母、数字和下划线组成，但是不能以数字开头。
- 等号两侧不能有空格
- 变量名称一般习惯为大写

**将命令的返回值赋给变量（重点）**
- A=\`ls -la\` 反引号，运行里面的命令，并把结果返回给变量 A
- A=$(ls -la) 等价于反引号

![](attachment/Pasted%20image%2020230913160442.png)

---

**设置环境变量**

基本语法
```txt
export 变量名=变量值 （功能描述：将 shell 变量输出为环境变量）
source  配置文件     （功能描述：让修改后的配置信息立即生效）
echo $变量名         （功能描述：查询环境变量的值）
```
![](attachment/Pasted%20image%2020230913161012.png)

**快速入门**

在/etc/profile 文件中定义 TOMCAT_HOME 环境变量
![](attachment/Pasted%20image%2020230913161050.png)

查看环境变量 TOMCAT_HOME 的值
```txt
echo $TOMCAT_HOME
```

在另外一个 shell 程序中使用 TOMCAT_HOME
![](attachment/Pasted%20image%2020230913161130.png)

注意：在输出 TOMCAT_HOME 环境变量前，需要让其生效
```txt
source /etc/profile
```

### 位置参数变量

当我们执行一个 shell 脚本时，如果希望获取到命令行的参数信息，就可以使用到位置参数变量，比如 ：` ./myshell.sh 100 200 `,  这个就是一个执行 shell 的命令行，可以在 `myshell`  脚本中获取到参数信息

基本语法
```txt
$n （功能描述：n 为数字，$0 代表命令本身，$1-$9 代表第一到第九个参数，十以上的参数，十以上的参数需要用大括号包含，如${10}）

$* （功能描述：这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体）

$@（功能描述：这个变量也代表命令行中所有的参数，不过$@把每个参数区分对待）

$#（功能描述：这个变量代表命令行中所有参数的个数）
```

**位置参数变量应用实例**
案例：编写一个 shell 脚本`positionPara.sh` ， 在脚本中获取到命令行的各个参数信息

![](attachment/Pasted%20image%2020230913161544.png)

![](attachment/Pasted%20image%2020230913161549.png)

### 预定义变量

预定义变量就是 shell 设计者事先已经定义好的变量，可以直接在 shell 脚本中使用

**基本语法**
```txxt
$$ （功能描述：当前进程的进程号（PID））

$! （功能描述：后台运行的最后一个进程的进程号（PID））

$？ （功能描述：最后一次执行的命令的返回状态。如果这个变量的值为 0，证明上一个命令正确执行；如果这个变量的值为非 0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确了。）
```

**应用实例**
在一个 shell 脚本中简单使用一下预定义变量
![](attachment/Pasted%20image%2020230913185359.png)


## 运算符

**基本语法**
```txt
“$((运算式))”或“$[运算式]”

expr m + n

注意 expr 运算符间要有空格

expr m - n

expr    \*, /, %        乘，除，取余
```

应用实例
案例 1：计算（2+3）X4 的值
`$((运算式))`
![](attachment/Pasted%20image%2020230913185702.png)

`$[运算式]`
![](attachment/Pasted%20image%2020230913185727.png)

`expr`
![](attachment/Pasted%20image%2020230913185746.png)

案例 2：请求出命令行的两个整数参数的和
![](attachment/Pasted%20image%2020230913185813.png)

## 条件判断

**if判断语句**

基本语法
```shell
[ condition ]
# 注意 condition 前后要有空格
# 非空返回 true，可使用$?验证（0 为 true，>1 为 false）
```

应用实例

```shell
[ atguigu ] # 返回 true 
[] # 返回 false

[condition] && echo OK || echo notok
# 条件满足，执行后面的语句
```

**常用判断条件**

两个整数的比较
- = 字符串比较
- -lt 小于
- -le 小于等于
- -eq 等于
- -gt 大于
- -ge 大于等于
- -ne 不等于

按照文件权限进行判断
- -r 有读的权限 \[ -r 文件 ]
- -w 有写的权限
- -x 有执行的权限

按照文件类型进行判断
- -f 文件存在并且是一个常规的文件
- -e 文件存在
- -d 文件存在并是一个目录

**应用实例**

案例 1："ok"是否等于"ok"
![](attachment/Pasted%20image%2020230913190720.png)

案例 2：23 是否大于等于 22
![](attachment/Pasted%20image%2020230913190745.png)

案例 3：/root/install.log 目录中的文件是否存在
![](attachment/Pasted%20image%2020230913190815.png)

## 流程控制

基本语法
```txt
if [ 条件判断式 ];
then
	程序
fi
```

```txt
if [ 条件判断式 ] 
then
	程序
elif [条件判断式]
then
	程序
fi
```

注意事项：
- \[ 条件判断式 ]，中括号和条件判断式之间必须有空格
- 推荐使用第二种方式

应用实例
案例：请编写一个 shell 程序，如果输入的参数，大于等于 60，则输出 "及格了"，如果小于 60，则输出 "不及格"
![](attachment/Pasted%20image%2020230913191114.png)

---

**case 语句**

基本语法
```txt
case $变量名 in "值 1"）
	如果变量的值等于值 1，则执行程序 1
;;
"值 2"）
	如果变量的值等于值 2，则执行程序 2
;;
…省略其他分支…
*）
	如果变量的值都不是以上的值，则执行此程序
;;
esac
```

应用实例
案例 1  ：当命令行参数是 1  时，输出 "周一",  是 2  时，就输出"周二"， 其它情况输出"other"

![](attachment/Pasted%20image%2020230913191336.png)


--- 

**for 循环**

基本语法1 
```txt
for 变量 in 值 1  值 2  值 3… 
do
	程序
done
```

应用实例
案例 1  ：打印命令行输入的参数  【会使用到$* $@】
![](attachment/Pasted%20image%2020230913191451.png)

基本语法 2
```txt
for (( 初始值;循环控制条件;变量变化 )) 
do
	程序
done
```

应用实例
案例 1 ：从 1 加到 100 的值输出显示
![](attachment/Pasted%20image%2020230913191609.png)

---

while 循环

基本语法 1
```txt
while [ 条件判断式 ] 
do
	程序
done
```

应用实例
例 1  ：从命令行输入一个数 n，统计从 1+..+ n  的值是多少？
![](attachment/Pasted%20image%2020230913191720.png)

## read 读取控制台输入

基本语法
```txt
read(选项)(参数)
```

选项
- -p：指定读取值时的提示符；
- -t：指定读取值时等待的时间（秒），如果没有在指定的时间内输入，就不再等待了

参数
- 变量：指定读取值的变量名

应用实例
案例 1：读取控制台输入一个 num 值
案例 2：读取控制台输入一个 num 值，在 10 秒内输入。
![](attachment/Pasted%20image%2020230913192009.png)

## 函数

shell 编程和其它编程语言一样，有系统函数，也可以自定义函数。系统函数中，我们这里就介绍两个。

### 系统函数

**basename 基本语法**

功能：返回完整路径最后 / 的部分，常用于获取文件名
```txt
basename [pathname] [suffix]
```
`basename [string] [suffix]`（功能描述：basename 命令会删掉所有的前缀包括最后一个（‘/’） 字符，然后将字符串显示出来。

选项：
- suffix 为后缀，如果 suffix 被指定了，basename 会将 pathname 或 string 中的 suffix 去掉。

--- 

**dirname 基本语法**
功能：返回完整路径最后 / 的前面的部分，常用于返回路径部分

dirname 文件绝对路径 （功能描述：从给定的包含绝对路径的文件名中去除文件名（非目录的部分），然后返回剩下的路径（目录的部分））

应用实例
案例 1：请返回 /home/aaa/test.txt 的 "test.txt" 部分
![](attachment/Pasted%20image%2020230913192538.png)

案例 2：请返回 /home/aaa/test.txt 的 /home/aaa
![](attachment/Pasted%20image%2020230913192548.png)

### 自定义函数

基本语法
```txt
[ function ] funname[()]
{
	Action; [return int;]
}
```

调用直接写函数名：`funname  [值]`

应用实例
案例 1：计算输入两个参数的和（read）， getSum
![](attachment/Pasted%20image%2020230913192714.png)

## Shell 编程综合案例
需求分析
1. 每天凌晨 2:10 备份 数据库 atguiguDB 到 /data/backup/db 2)
2. 备份开始和备份结束能够给出相应的提示信息
3. 备份后的文件要求以备份时间为文件名，并打包成 .tar.gz 的形式，比如：`2018-03-12_230201.tar.gz`
4. 在备份的同时，检查是否有 10 天前备份的数据库文件，如果有就将其删除。

编写一个 shell 脚本。
思路分析：
![](attachment/Pasted%20image%2020230913192824.png)

代码实现：
![](attachment/Pasted%20image%2020230913192833.png)

![](attachment/Pasted%20image%2020230913192839.png)

![](attachment/Pasted%20image%2020230913192843.png)

