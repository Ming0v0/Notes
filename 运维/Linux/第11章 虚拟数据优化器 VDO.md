
# 什么是虚拟数据优化器 VDO

VDO全称是Virtual Data Optimize（虚拟数据优化），主要是为了节省硬盘空间。 VDO叫重删压缩卷，可现实空间超分配存储。

- 红帽企业Linux8使用vdo功能，可以优化块设备上数据空间占用问题，它可以减少块设备上的磁盘使用空间，同时最大限度减小数据重复，从而节省磁盘空间。vdo包含两个模块: kvdo用于控制数据压缩，uds用于重复数据删除
- vdo层位于现在块设备(RAID或者本地磁盘)之上，存储层(如LVM和文件系统)位于vdo之上。
- 
下图显示了KVM虚拟机架构中，vdo所处的位置
![](attachment/Pasted%20image%2020231216212011.png)

## 创建VDO卷的方法


安装软件包（默认已安装）
```txt
yum -y install vdo kmod-kvdo
```

**创建vdo卷的命令**

```txt
vdo create --name=vdo名称 --device=用于创建VDO卷的磁盘路径及名称 --vdoLogicalSize=创建的VDO卷 的大小
```

例如：创建一个名称为vdo0，路径为`/dev/sdb1`，大小为10T的卷

```txt
vdo create --name=vdo0 --device=/dev/sdb1 --vdoLogicalSize=10T
```


- VDO卷的磁盘的物理大小，**最少要有5G**，小于5G就不能创建VDO卷的。 
- 那些存储介质可以作为VDO的物理卷：磁盘、分区、raid、lun


## 查看vdo卷

```
vdo status[--name=vdo名称]
```

查看文件系统使用情况

```txt
df -h
```

查看vdo卷列表

```txt
vdo list
```

启动和停止vdo卷

```txt
vdo start|stop -n vdo卷名称
```

用`vdostatus`查看卷的状态

```txt
vdostatus --human--readble
```

将vdo1格式化为xfs文件系统并挂载于`/mnt/vdo0`

```txt
mkfs.xfs /dev/mapper/vdo01
```

![](attachment/Pasted%20image%2020231216214214.png)


## vdo挂载

**临时挂载**
将vdo1挂载到/vdo1目录上。
```txt
mkdir -p /mnt/vdo1
mount /dev/mapper/vdo1 /mnt/vdo1
```

**永久挂载**

```txt
vim /etc/fstab
```

```txt
/dev/mapper/vdo1 /mnt/vdo1 xfs defaults,_netdev 0 0
```

- 注意：这里一定要有`_netde`v选 项，否则重启系统时，系统是启动不起来的。
- 挂载选项`x-systemd.requires=vdo.service`可延迟挂载文件系统，直到vdo.service启动为止

## 查看vdo的空间使用情况
