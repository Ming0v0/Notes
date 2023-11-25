
# VLAN基本概念

## 什么是VLAN

虚拟局域网(`Virtual Local Area Network，VLAN`)是将一个物理的局域网在逻辑上划分成多个广播域的技术。通过在交换机上配置VLAN，实现在同一个VLAN内的主机可以相互通信，而不同VLAN间的主机被相互隔离。

![](attachment/Pasted%20image%2020231125100446.png)

**定义**： (`Virtual Local Area Network，VLAN`)也称为虚拟局域网。

**功能**：隔离广播域，限制广播域的范围，减少广播流量。

**VLAN的原理**
- 同一个VLAN内的主机共享同一个广播域
- 同一个VLAN可以直接进行二层通信
- VLAN间的主机属于不同的广播域
- VLAN间的主机无法实现二层通信

## VLAN帧格式

![](attachment/Pasted%20image%2020231125100725.png)


类型：TAG和UNTAG，TAG是带有VLAN标记的以太网帧（Tagged Frame），UNTAG是没有带VLAN标记的标准以太网帧（Untagged Frame）。


带有VLAN标记的以太网帧中，TAG标签长度为4字节，具体内容说明如下。

TPID：Tag Protocol Identifier，2字节，**固定取值**，0x8100，是IEEE定义的新类型，**表明这是一个携带802.1Q标签的帧**。如果不支持802.1Q的设备收到这样的帧，会将其丢弃。

TCI：Tag Control Information，2字节，用来表示帧的控制信息，包括以下几个部分。

- Priority：3比特，**表示帧的优先级**，取值范围为0～7，值越大优先级越高。当交换机阻塞时，优先发送优先级高的数据帧。

- CFI：Canonical Format Indicator，1比特。**CFI表示MAC地址是否是经典格式。** CFI为0说明是经典格式，CFI为1表示为非经典格式。用于区分以太网帧、FDDI（Fiber Distributed Digital Interface）帧和令牌环网帧。在以太网中，CFI的值为0。

- VLAN Identifier：**VLAN ID，12比特，交换机一般可以划分255个VLAN，每个VLAN的ID 可以是1~4094之间的任意数字，ID的作用就是用于区分不同VLAN**，可以设置TAG和UNTAG属性，让交换机端口的下行或上行数据帧标记标签。

## VLAN在实际网络中的应用

在常见的企业园区网设计中，公司会为每一个部门创建一个VLAN，各自形成一个广播域，部门内部员工之间能够通过二层交换机直接通信，不同部门的员工之间必须通过三层IP路由功能才可以相互通信。


![](attachment/Pasted%20image%2020231125101107.png)


通过对两栋楼互联交换机的配置，可以实现为两栋楼工作的财务部创建VLAN10，技术部创建VLAN20，不仅实现了部门间的二层广播隔离，还实现了部门跨交换机的二层通信。

## 划分VLAN的方法

![](attachment/Pasted%20image%2020231125101259.png)

基于端口划分：根据交换机的端口编号来划分VLAN。

- 初始情况下，交换机的端口都处于VLAN1中，管理员通过为交换机的每个端口配置不同的PVID，将不同端口划分到不同的VLAN中，该方法是最常用的方法。

基于MAC地址划分：根据主机网卡的MAC地址划分VLAN。

- 此划分方法需要网络管理员提前配置网络中的主机MAC地址和VLAN ID的映射关系。如果交换机收到不带标签的数据帧，会查找之前配置的MAC地址和VLAN映射表，然后根据数据帧中携带的MAC地址来添加相应的VLAN标签。

基于IP子网划分：交换机在收到不带标签的数据帧时，根据报文携带的IP地址给数据帧添加VLAN标签。

基于协议划分：根据数据帧的协议类型（或协议族类型）、封装格式来分配VLAN ID。网络管理员需要先配置协议类型和VLAN ID之间的映射关系。

基于策略划分：使用几个条件的组合来分配VLAN标签。这些条件包括IP子网、端口和IP地址等。只有当所有条件都匹配时，交换机才为数据帧添加VLAN标签。另外，针对每一条策略都是需要手工配置的。

## 交换机端口的分类

**Access（接入）端口**
- Access端口用于连接计算机等终端设备，只能属于一个VLAN，也就是只能传输一个VLAN的数据。
- Access端口在发送出站数据帧之前，**会判断这个要被转发的数据帧中携带的VLAN ID是否与出站端口的PVID相同**，若相同则去掉VAN标签进行转发；若不同则丢弃。

**Trunk（干道）端口**
- Trunk端口用于连接交换机等网络设备，它允许传输多个VLAN的数据。
- Trunk端口在发送出站数据帧之前，**会判断这个要被转发的数据帧中携带的VLAN ID是否与出站端口的PVID相同，若相同则去掉VLAN标签进行转发；若不同则判断本端口是否允许传输这个数据帧的VLAN ID，若允许则转发（保留原标签），否则丢弃。**

**Hybrid（混合）端口**
- Hybrid接口是华为系列交换机端口的默认工作模式，它能够接收和发送多个VLAN的数据帧，可以用于链接交换机之间的链路，也可以用于连接终端设备。
- Hybrid接口兼具Access接口和Trunk接口的特征，在实际应用中，可以根据对端接口工作模式自动适配工作。

# VLAN配置

## 配置Access端口和Trunk端口

- 配置Access，执行 `port link-type access` 命令

- 配置Trunk ，执行 `port link-type trunk` 命令

案例：为修改交换机的`Ethernet 0/0/1`端口为access模式，并配置端口的PVID为VLAN10，同时修改交换机的`Ethernet 0/0/2`端口为trunk模式，配置允许VLAN10、VLAN20通过

![](attachment/Pasted%20image%2020231125101907.png)

当修改端口为access模式后，需要配合`port default vlan <vlan-id>`命令，配置端口的PVID；

当修改端口为trunk后，需要使用`port trunk allow-pass vlan { vlan-id1 [ to vlan-id2 ] }`命令，配置trunk干道允许哪些VLAN通过。


## 检查VLAN信息

创建VLAN后，可以执行`display vlan`命令验证配置结果。如果不指定任何参数，则该命令将显示所有VLAN的简要信息；

执行`display vlan [vlan-id [verbose ]]`命令，可以查看指定VLAN的详细信息，包括VLAN ID、类型、描述、VLAN的状态、VLAN中的端口、以及VLAN中端口的模式等；

执行`display vlan vlan-id statistics`命令，可以查看指定VLAN中的流量统计信息；

执行`display vlan summary`命令，可以查看系统中所有VLAN的汇总信息。

案例：为使用 `display vlan` 命令查看交换机已创建的VLAN信息
![](attachment/Pasted%20image%2020231125102826.png)

# 常见场景下的VLAN配置

## 案例：单交换机场景

**案例背景与要求**
为财务部创建VLAN10，PC1和PC2为财务部PC，连接在交换机的ETH0/0/1和ETH0/0/2端口，为项目部创建VLAN20，PC3和PC4为项目部PC，连接在交换机的ETH0/0/3和ETH0/0/4端口。实现两个部门内部PC可以通信，跨部门PC不能互相通信。

案例拓扑图
![](attachment/Pasted%20image%2020231125103034.png)
案例配置过程
创建VLAN 10和VLAN 20。
![](attachment/Pasted%20image%2020231125103115.png)

将连接PC的交换机端口配置为access模式，并加入到相应的VLAN中，以PC1为例。
![](attachment/Pasted%20image%2020231125103138.png)

在交换机上使用`display port vlan`命令查看各端口的模式
![](attachment/Pasted%20image%2020231125103203.png)


## 案例：跨交换机场景

案例背景与要求

以Jan16公司的财务部和项目部为例，为财务部创建VLAN10，PC1和PC2为财务部PC，连接在交换机SW1的ETH0/0/1和交换机SW2的ETH0/0/1端口，为项目部创建VLAN20，PC3和PC4为项目部PC，连接在交换机SW1的ETH0/0/2和交换机SW2的ETH0/0/2端口，配置交换机互联的端口模式为Trunk，实现两个部门内部可以通信，跨部门的PC不能互相通信。

![](attachment/Pasted%20image%2020231125103305.png)
