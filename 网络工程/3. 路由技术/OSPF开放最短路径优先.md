OSPF：**Open Shortest Path First，开放最短路径优先**
 - 典型的链路状态路由协议，是目前业内使用非常广泛的IGP协议之一。
 - 版本： IPv4-OSPFv2（RFC 2328）；IPv6-OSPFv3（RFC 2740）。
 - 交互LS（Link State，链路状态）信息，而不是直接交互路由。
 - 使用组播（224.0.0.5) 和 224.0.0.6）。
 - 收敛较快。
 - 以接口开销作为度量值。
 - 采用的SPF算法可以有效的避免环路。
 - 触发式更新（以较低的频率（每30分钟）发送定期更新，被称为链路状态泛洪）
 - 不支持自动汇总，支持手动汇总。
 - 多区域的设计使得OSPF能够支持更大规模的网络。

==应用场景==
![](attachment/Pasted%20image%2020250314095350.png)

**OSPF区域：**

| **术语**                     | **备注**                                                                 |
| -------------------------- | ---------------------------------------------------------------------- |
| **区域（Area）**               | 为了适应大型的网络，OSPF在AS内划分多个区域<br>区域是以接口为单位来划分的<br>每个OSPF路由器只维护所在区域的完整链路状态信息 |
| **区域** **ID（Area** **ID）** | 可以表示成一个十进制的数字，如：1 也可以表示成一个IP地址，如：0.0.0.1                               |
| **区域优点**                   | 尽量减少路由表条目，使拓扑变化仅影响本区域内部                                                |

 ==区域类型==

| 类型          | 备注                    |
| --------- | ------------------- |
| **骨干区域**  | Area 0，也称为：传输区域     |
| **非骨干区域** | 非Area 0，也称为：标准区域    |
| **特殊区域**  | Stub、 NSSA等，优化LSA传递 |
| **PS**    | 所有非骨干区域必须和骨干区域直接相连  |

![](attachment/Pasted%20image%2020250314100255.png)

![](attachment/Pasted%20image%2020250314100344.png)


==路由器类型==

|**类型**|**备注**|
|---|---|
|**IR**|Internal Router，内部路由器<br><br>所有的接口都属于同一区域|
|**BR**|Backbone Router，骨干路由器<br><br>至少有一个接口属于骨干区域|
|**ABR**|Area Border Router，区域边界路由器<br><br>连接一个或者多个区域到骨干区域，至少有一个接口属于骨干区域|
|**ASBR**|Autonomous System Border Router，自治系统边界路由器<br><br>把从其他路由协议学习到的路由以引入的方式到OSPF进程中|
|**PS**|一台路由器可以同时属于多种类型|

![](attachment/Pasted%20image%2020250314100357.png)

![](attachment/Pasted%20image%2020250314100406.png)


# OSPF核心工作流程：链路状态工作流程

1.  发现并建立邻居
2.  邻居之间交互链路状态信息并同步LSDB（链路状态数据库、地图）
3.  使用SPF算法计算到每个目标网络的最短距离
4.  生成路由表项加载路由表中

![](attachment/Pasted%20image%2020250314101847.png)

![](attachment/Pasted%20image%2020250314101903.png)

**OSPF三张表：**

|**表名称**|**备注**|
|---|---|
|**邻居表**|记录所有邻居信息|
|**链路状态数据库表（LSDB）**|记录所有链路状态信息|
|**路由表**|记录最佳路由|
![](attachment/Pasted%20image%2020250314102603.png)

![](attachment/Pasted%20image%2020250314102619.png)

![](attachment/Pasted%20image%2020250314102625.png)



**Router** **ID：**

- 运行OSPF协议前，必须选取一个RID（非0.0.0.0)
- 用来唯一标识一台OSPF路由器
- RID可以手动配置，也可以自动生成，选取规则如下：

| **规则**      | **备注**                                              |
| ----------- | --------------------------------------------------- |
| **RID选取顺序** | 1. 手动配置（推荐）<br>2. 回环接口上选取最大IP地址<br>3. 物理接口上选取最大IP地址 |
| **PS**      | 一旦选取成功，不会更改，除非删除了原先的RID或者重启OSPF进程                   |
![](attachment/Pasted%20image%2020250314102709.png)
**PS：在OSPF网络设计和实施中，考虑的第一点就是Router ID的选择。**
  

**OSPF报文结构和类型：封装于IP协议之上， IP协议号 = 89**
![](attachment/Pasted%20image%2020250314102749.png)

![](attachment/Pasted%20image%2020250314102804.png)


|**数据包类型**|**备注**|
|---|---|
|**Hello**|发现和维护邻居|
|**DD/DBD**|LSDB的摘要信息，用于对比和同步LSDB|
|**LSR**|请求所需要的LSA|
|**LSU**|发送所需要的LSA|
|**LSAck**|确认收到的LSA|

**OSPF状态机制：**
![](attachment/Pasted%20image%2020250314102817.png)

|**状态**|**备注**|
|---|---|
|**Down（失效状态）**|没有收到Hello包|
|**Init（初始状态）**|收到Hello包，但没有看到自己|
|**Two-Way（双向通讯状态）**|收到Hello包，且看到了自己，形成邻居关系|
|**ExStart（交换初始状态）**|协商主/从关系，保证DD包能有序的发送|
|**ExChange（交换状态）**|交换DD包，对比LSDB|
|**Loading（加载状态）**|正在同步LSDB， LSR和LSU交换|
|**Full（完全邻接状态）**|LSDB完成同步，形成邻接关系|
|**PS**|只有Two-Way和Full是稳定状态|

**OSPF工作流程（数据包和状态切换过程）：**
![](attachment/Pasted%20image%2020250314103012.png)

![](attachment/Pasted%20image%2020250314103020.png)

![](attachment/Pasted%20image%2020250314103026.png)

![](attachment/Pasted%20image%2020250314103054.png)

![](attachment/Pasted%20image%2020250314103036.png)

![](attachment/Pasted%20image%2020250314103044.png)

**OSPF邻居建立条件：**

|**条件**|**要求**|
|---|---|
|**RID**|唯一|
|**Hello/Dead定时器**|一致|
|**Area** **ID**|一致|
|**认证**|一致|
|**MTU**|一致（华为默认不检查）|
|**子网掩码**|一致（除了点到点和虚连接）|
|**网络地址**|一致|
|**Option**|一致|

![](attachment/Pasted%20image%2020250314103138.png)

**OSPF网络类型**：**一种接口变量**，这个变量将影响OSPF在接口上的操作，如：发送报文的方式，是否选举DR、 BDR等。

![](attachment/Pasted%20image%2020250314103250.png)

![](attachment/Pasted%20image%2020250317154039.png)

![](attachment/Pasted%20image%2020250317154046.png)

![](attachment/Pasted%20image%2020250317154051.png)

**DR和BDR：**
•  在MA（BMA和NBMA）网络类型，为了减少邻接关系的数量，从而减少数据包交换次数，最终节省带宽 ，降低对路由器处理能力的压力，选举DR和BDR。


| **术语**      | **备注**                                                                                                                                   |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **DR**      | Designed Router，指定路由器，类似：老大、班长、总经理                                                                                                       |
| **BDR**     | Backup DR，备份DR，类似：二当家、副班长、副总经理                                                                                                           |
| **DRother** | 类似：普通学生、普通员工                                                                                                                             |
| **关系**      | DR、BDR、DRothers之间建立邻接关系（Full） DRothers之间仅建立邻居关系（Two-Way）                                                                                 |
| **地址**      | DR和BDR监听224.0.0.6<br>所有OSPF路由器监听224.0.0.5                                                                                                |
| **选举规则**    | 首先比较Hello报文中携带的优先级<br>• 范围：0~255，默认=1 ，0不参与选举<br>• 最高的被选举为DR ，次高的被选举为BDR 优先级一致的情况下，比较RID，越大越优先<br><br>选举具有非抢占性，终身制，除非当DR和BDR都失效或重启OSPF进程 |

![](attachment/Pasted%20image%2020250317154108.png)



**选举制：**
![](attachment/Pasted%20image%2020250317154123.png)

**终身制：**
![](attachment/Pasted%20image%2020250317154132.png)

**继承制：**
![](attachment/Pasted%20image%2020250317154149.png)

**拓扑变化：**
![](attachment/Pasted%20image%2020250317154426.png)

![](attachment/Pasted%20image%2020250317154432.png)


**OSPF度量值：Cost，开销**

•  在每一个运行OSPF的接口上，都维护着一个接口Cost

•   **Cost** **=** `10^8/BW（bps）= 100Mbps/BW = 接口带宽参考值/接口带宽`

•  **计算到一个目标网络的度量值** =

○ 从源到目标沿途所有出站接口的Cost值累加（数据方向）

○ 从源到本路由器沿途所有入站接口的Cost值累加（路由方向）

![](attachment/Pasted%20image%2020250317154527.png)

![](attachment/Pasted%20image%2020250317154533.png)

![](attachment/Pasted%20image%2020250317154539.png)



**OSPF配置：**

```txt
ospf 1 router-id 1.1.1.1    // 创建OSPF进程，手动配置RID
  area 0 | 0.0.0.0          // 配置区域
    network 192.168.0.0 0.0.0.255   // 宣告网络，即指定运行OSPF的接口
  bandwidth-reference 1000  // 调整带宽参考值，默认=100Mbps
```
PS：宣告使用反掩码

```txt
display ospf peer[brief]  // 显示OSPF邻居信息
```

```txt
interface g0/0/1  // 进入接口
  ospf timer hello 10  // 修改Hello包发送间隔
  ospf timer dead 40  // 修改Hello包超时时间
  ospf dr-priority 100 // 修改OSPF接口优先级，默认=1
  ospf cost 10  // 修改OSPF接口开销
```

```txt
display ospf interface g0/0/1  // 显示OSPF接口信息
```

```txt
reset ospf process  // 重启OSPF进程
```

==**配置案例：**==
![](attachment/Pasted%20image%2020250317160417.png)

![](attachment/Pasted%20image%2020250317160803.png)

![](attachment/Pasted%20image%2020250317160921.png)

![](attachment/Pasted%20image%2020250317160926.png)

![](attachment/Pasted%20image%2020250317160931.png)

# 路由认证

技术背景：
![](attachment/Pasted%20image%2020250317161620.png)

OSPF认证命令：

```txt 
ospf authentication-mode md5 1 cipher huawei  // 配置接口认证
```

```txt
authentication-mode md5 1 cipher wakin  // 配置区域认证
```

PS：如果同时配置，接口认证优先生效

![](attachment/Pasted%20image%2020250317161719.png)

# 缺省路由发布

缺省路由配置方式：

静态配置使用命令：
```txt
ip route-static 0.0.0.0 0.0.0.0
```

动态发布
```txt
使用动态路由协议进行发布
```

OSPF缺省路由发布命令：

```txt
default-route-advertise [always]   // 发布缺省路由
```

always：不需要路由表事先存在缺省路由（强制发布）

![](attachment/Pasted%20image%2020250317162041.png)

![](attachment/Pasted%20image%2020250317162047.png)

