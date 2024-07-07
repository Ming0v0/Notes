# 创建NFS共享

NFS：`Network Filesystem 网络文件系统`
前面讲的是本地存储，这里讲的是如何使用网络上的存储设备，NFS是网络文件系统。
- 用于在linux或者UNIX之间进行文件共享的一种机制，NFS
- Windows和LINUX之间进行文件共享，CIFS

## NFS配置

安装nfs-utils程序包：`yum -y install nfs-utils` （Linux、UNIX无论用到什么发行版本，默认是自带的。）

查看是否已安装nfs-utils包。`rpm -q nfs-utils`

### **第1步，在服务端上创建用于共享的目录**

在根目录下创建share目录
```txt
[root@localhost ~]mkdir /share
```

### **第2步，将目录设置为共享，通过修改配置文件实现**

打开配置文件 
```txt
[root@localhost ~]vim /etc/exports 
```

编辑共享的选项，保存退出
```
/share *(rw,sync) // 配置文件内容
```

![](attachment/Pasted%20image%2020231217000435.png)

### **第3步，启动nfs-server.service服务，查看共享情况**

启动服务
```txt
[root@localhost ~]systemctl restart nfs-server.service 
```

查看共享情况
```txt
[root@localhost ~]exportfs -v 
```

```txt
/share <world>(sync,wdelay,hide,no_subtree_check,sec=sys,rw,secure,root_squash,no_all_squash)
```

![](attachment/Pasted%20image%2020231217000642.png)

### **第4步，先关闭防火墙**

先临时关闭防火墙（不推荐）
```txt
[root@localhost ~]systemctl stop firewalld.service 
```

设置开机不启动（不推荐）
```txt
[root@localhost ~]systemctl disable firewalld.service 
```

```txt
Removed /etc/systemd/system/multi-user.target.wants/firewalld.service. 
Removed /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service
```

## 挂载NFS共享

### **第5步，打开另外一台主机，进行验证测试**

首先查看是否安装nfs，`rpm -q nfs-utils `

查看服务端共享了哪些目录：`showmount -e 192.168.88.132` ，再把服务端的目录，挂载到本地。

![](attachment/Pasted%20image%2020231217001911.png)

挂载还可以这样书写： `mount 192.168.88.132:/share /mnt/`

**永久挂载**

```txt
[root@localhost ~]vim /etc/fstab 
```

```txt
192.168.88.132:/share /mnt nfs defaults 0 0 mount -a
```


### **第6步，在服务端的共享目录下创建文件，然后再去本机查看挂载目录，进行进一步的验证**

比如：在服务端上创建一些文件，查看是否可以访问。 另外，在客户端能否用服务端的共享文件写入数据呢

![](attachment/Pasted%20image%2020231217002226.png)


#### **解决在客户端的共享目录下创建文件的问题。**

解决的办法

在服务端上完成共享目录权限的设置
![](attachment/Pasted%20image%2020231217002548.png)

在客户端进行测试，发现可以创建文件和目录。
![](attachment/Pasted%20image%2020231217002638.png)

那为什么要映射呢？为什么要把客户端的root映射成为服务端的nobody用户呢？ 其实就是**为了安全，因为客户端的root权限太大**，会产生很多不案例的因素。

比如：在服务端创建的文件，在服务端打开时，可以查看文件内容，但是不能对里面的文件进行编辑。 这是映射的结果，如果没有映射，则客户端可以随便编辑共享目录里面的文件，就会产生很多不安全。

![](attachment/Pasted%20image%2020231217003314.png)

如果不映射，会如何呢？会导致客户端的root权限很大，随便修改共享出来的文件。验证如下： 
（1）先在客户端上卸载挂载
```txt
[root@localhost ~]umount /mnt
```

（2）在服务端上打开配置文件，将配置选项设置为：加一个选项`no_root_squash`

```txt
[root@localhost ~]vim /etc/exports
```

```
/share *(rw,sync,no_root_squash) // 这个参数是指不映射root
```

（3）再次激活共享目录。
```txt
[root@localhost ~]exports -r  // 让配置文件生效
```

（4）回到客户端，重新挂载目录
![](attachment/Pasted%20image%2020231217004106.png)

（5）在该目录下，创建文件或目录，以列表方式查看文件信息时，发现是root用户了。
![](attachment/Pasted%20image%2020231217004140.png)


## NFS服务端配置选项

在服务端共享时，可以设置共享权限，编辑配置文件`/etc/exports`
`/share 192.168.40.0/24(共享权限)`

![](attachment/Pasted%20image%2020231217004606.png)

距离远，不适合同步模式，适合异步模式，异步实现的是周期性同步。

## NFS客户端配置选项

在客户端挂载时，也可以指定挂载选项
`192.168.40.10:/share /data nfs defaults,(挂载选项) 0 0`

![](attachment/Pasted%20image%2020231217004810.png)

例：以只读方式挂载
```
[root@localhost ~]mount -o ro 192.168.88.132:/share /mnt/ 
```

# autofs自动按需挂载

自动挂载的意思是，把一个外部设备`/dev/xx`和某个目录`/dir/yy`关联起来。

平时`/dev/xx`是否挂载到了`/dir/yy`上不需要考虑，但是访问`/dir/yy`时，系统就知道要访问`/dev/xx`中的数据，这时系统自动将`/dev/xx`挂载到`/dyr/yy`。 

- 自动挂载程序是一个服务（autofs），它可以根据需要自动挂载文件系统，如自动挂载NFS共享、 或文件系统。不需要时，将自动卸载。大概10-15分钟。

**自动挂载的优势**

1. **自动挂载的NFS共享可以供计算机上的所有用户使用**
2. NFS共享不像`/etc/fstab`中的条目一样永久挂载，从而可以释放网络和系统资源
3. **自动挂载是在客户端**，不需要服务器端配置
4. NFS是默认的文件系统的挂载，但也可以用来自动挂载其他的文件系统
5. autofs是一种服务，像其他系统服务一样管理

- 查看服务是否启动。`systemctl status autofs.service`
- 设置服务开机自启。`systemctl enable autofs.service`
- 查看开机状态。`systemctl is-enabled autofs.service`
- autofs的配置文件：主配置文件`/etc/auto.master`，（`/etc/auto.master.d/` 这个是一个目录）

**第1步，安装autofs软件包**：`yum install autofs -y`

**第2步，启动服务**：`systemctl enable --now autofs`
![](attachment/Pasted%20image%2020231217005905.png)
如果服务没有启动，还要执行start，先启动。

**第3步，编辑autofs配置文件**

编辑配置文件：`/etc/auto.master`，或者在`/etc/auto.master.d/`下创建一个以.autofs结尾文件。

下面以`/etc/auto.master`为例：
```txt
[root@localhost ~]vim /etc/auto.master
```

![](attachment/Pasted%20image%2020231217010257.png)

```txt
[root@localhost ~]/data /etc/cdrom.misc
```

- `/data` 这个是一个路径， 这个路径是挂载点的上一级目录叫基础目录。
- `/etc/cdrom.misc` 在基础目录中触发的挂载配置文件。

这个语句的意思是：当我们进入到一个叫`/data`目录 时，就会触发`/etc/cdrom.misc`的配置文件。 所以，我们要实现的挂载，应该去`/etc/cdrom.misc` 配置文件里面去设置。

**第4步，编辑触发文件的内容**

触发文件是第3步中在配置文件里面添加的那个文件。
![](attachment/Pasted%20image%2020231217010821.png)

即 先在根目录下创建一个data目录。 
打开第三步对应的文件，即打开`/etc/cdrom.misc`文件， 并写上配置内容：

![](attachment/Pasted%20image%2020231217010847.png)

```txt
cdrom -fstype=iso9660，ro :/dev/cdrom
```

- `cdrom` 挂载点， 其实就是 `/data/cdrom`这个目录
- `-fstype=iso9660` 挂载文件系统的类型
- `ro` 权限只读
- `:/dev/cdrom` 真正挂载的设备路径或文件系统

也可以写`cdrom -ro :/dev/cdrom`

**第5步，重启autofs.service服务**

```txt
[root@localhost ~]systemctl restart autofs.service
```

验证 进入/data目录，然后再`cd cdrom`，就立马触发。
