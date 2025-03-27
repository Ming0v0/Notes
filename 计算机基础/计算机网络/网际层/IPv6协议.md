
# IPv4对比IPv6

**IPv4局限性**
经过多年的发展，已非常成熟，易于实现，得到所有厂商IPv4是目前广泛部署的互联网协议，设备的支持，但也有一些不足之处。
- 能够提供的地址空间不足且分配不均
- 互联网骨干路由器的路由表非常庞大

| 版本 | 长度   | 地址数量                                        |
| ---- | ------ | ----------------------------------------------- |
| IPv4 | 32bit  | 4,294,967,296                                   |
| IPv6 | 128bit | 340,282,366,920,938,463,374,607,431,768,211,456 |

**IPv6优势**

IPv6采用128位地址长度，其地址数量总数可达2128个，它能让地球上每一粒沙子都可拥有一个IP地址。这不但解决了网络地址资源数量的问题，同时也为万物互联所限制的IP地址数量扫清了障碍。因此，相比IPv4，IPv6具有诸多优点

- 地址空间巨大
- 层次化的路由设计
- 效率高，扩展灵活
- 支持即插即用
- 更好的安全性保障
- 引入了流标签的概念


![](attachment/Pasted%20image%2020231120201057.png)

# IPv6数据包封装

**IPv6基本报头**

- 不同于IPv4报头长度为`20~60Byte`，IPv6基本报头是定长`40Byte`，其中包含8个字段

![](attachment/Pasted%20image%2020231120201149.png)

**IPv6基本报头字段解释**

- Version:4bit，指定IPv6，数值-6
- Traffic Class:8bit，用来区分不同类型或优先级的IPv6数据包
- Flow Label: 20bit，用作标识同一个数据流，此字段为IPv6新增字段Payload Length:16bit，数据包的有效载荷
- Next Header:8bit，指明跟在基本报头后面是哪种扩展报头或者上层协议中的协议类型:
- Source Address:128bit，数据包的源IPv6地址，必须是单播地址
- Destination Address:128bit，数据包的目标IPv6地址，可以是单播或组播地址

常见上层协议及对应的Next Header值，其中包含6个IPv6扩展报头，4个IPv6相关协议

![](attachment/Pasted%20image%2020231120201445.png)

**IPv6扩展报头**

- 逐跳选项扩展报头和目的选项扩展报头的数据部分都采用类型-长度-值 (TV)的选项设计。
![](attachment/Pasted%20image%2020231120201733.png)

**IPv6扩展报头字段解释**

- option Type：8bit，标识类型，最高2位表示当设备部识别此扩展报头时的处理方法
- Opt Data Len：8bit，标识Option Data部分的长度，最大为255，单位是Byte，不包含Option Type和OptData Len部分的长度。包含选项的具体数据内容
- Option Data：长度可变，最大为255Byte

IPv6地址表示方式`(128,16,1(16)=4(2),32(16))`
- 对IPv6来说，将每16位分成1块，一共分为8块，每块用“.”相隔，如下是IPv6地址的完整表达
![](attachment/Pasted%20image%2020231120202240.png)

# IPv6地址表示方式

**IPv6地址表示方式的简化规则**

每一个地址块起始部分0可省略
![](attachment/Pasted%20image%2020231120202429.png)

有1个或连续多个0组成的地址块可用"::"取代
![](attachment/Pasted%20image%2020231120202524.png)

**IPv6地址表示方式注意事项**
在整个地址中，只能出现一次"::"，如以下完整IPv6地址`2001:0000:0000:0001:0000:0000:0000:0001`

错误的简化表达
`2001::1::1`

可正确表示为以下2种表达方式
表达方式1：`2001::1:0:0:0:1`
表达方式2：`2001:0:0:1::1`

**IPv6地址网络位和主机位**

IPv6地址也分为两部分：网络位和主机位，为了区分这两部分，在IPv6地址后面加上"/数字(+进制)"的组合，数字用来确定从头开始的几位是网络位。
- 例`[2001::1/64]`

# IPv6地址配置

![](attachment/Pasted%20image%2020231120214351.png)

```txt
AR1
#
 sysname R1
#
ipv6 
#
interface GigabitEthernet0/0/0
 ipv6 enable 
 ipv6 address 2001::2/64 
#
interface GigabitEthernet0/0/1
 ipv6 enable 
 ipv6 address 2000::2/64 
#
ipv6 route-static :: 0 2000::1 
ipv6 route-static 2002:: 64 2000::1 
#
```

```txt
AR2
#
 sysname R2
#
ipv6 
#
interface GigabitEthernet0/0/0
 ipv6 enable 
 ipv6 address 2000::1/64 
#
interface GigabitEthernet0/0/1
 ipv6 enable 
 ipv6 address 2002::2/64 
#
ipv6 route-static :: 0 2000::2 
#
```
