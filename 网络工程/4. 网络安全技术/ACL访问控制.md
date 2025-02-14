
# ACL基本概念

访问控制列表ACL (Access Control List) 是由一系列规则组成的集合，ACL通过这些规则对报文进行分类，从而使设备可以对不同类报文进行不同的处理。

一个ACL通常由若干条“deny | permit”语句组成，每条语句就是该ACL的一条规则，每条语句中的“deny | permit”就是与这条规则相对应的处理动作。

- 处理动作“permit”的含义是“允许”
- 处理动作“deny”的含义是“拒绝”

ACL是一种应用非常广泛的网络安全技术，配置ACL网络设备的工作过程可分为以下两个步骤
- 根据事先设定好的报文匹配规则对经过该设备的报文进行匹配
- 对匹配的报文执行事先设定好的处理动作

## ACL规则

ACL负责管理用户配置的所有规则，并提供报文匹配规则的算法。

ACL规则管理基本思想如下

- 每个ACL作为一个规则组，一般可以包含多个规则

- ACL中的每一条规则通过规则ID（rule-id）来标识，规则ID可以自行设置，也可以由系统根据步长自动生成，即设备会在创建ACL的过程中自动为每一条规则分配一个ID

> 规则ID之间会留下一定的间隔。
>
> 例如，将规则编号的步长设定为10（注：规则编号的步长的默认值为5），则规则编号将按照10、20、30、40 …这样的规律自动进行分配。如果将规则编号的步长设定为2，则规则编号将按照2、4、6、8 …这样的规律自动进行分配。步长的大小反映了相邻规则编号之间的间隔大小。
> 
> 间隔的存在，实际上是为了方便在两个相邻的规则之间插入新的规则。

- 默认情况下，ACL中的所有规则均按照规则ID从小到大的顺序与规则进行匹配

 - 规则ID之间会留下一定的间隔。如果不指定规则ID时，具体间隔大小由“ACL的步长”来设定。

## ACL规则匹配

1. 配置了ACL的设备在接收到一个报文之后，会将该报文与ACL中的规则**逐条进行匹配**
2. 如果不能匹配上当前这条规则，则会继续尝试去匹配下一条规则
3. 一旦**报文匹配上了某条规则**，则会对该报文执行这条规则中定义的处理动作（permit或deny），并且**不再继续尝试与后续规则进行匹配**
4. 如果报文不能匹配上ACL的任何一条规则，则设备会对该报文执行“permit”这个处理动作。

在将一个数据包和访问控制列表的规则进行匹配的时候，由规则的匹配顺序决定规则的优先级。华为设备支持以下两种匹配顺序。

- **匹配顺序按照用户配置ACL规则的先后序列进行匹配，先配置的规则先匹配。** 根据ACL中语句的顺序，把数据包和判断条件进行比较。一旦匹配，就采用语句中的动作并结束比较过程，不再检查以后的其他条件判断语句。如果没有任何语句匹配，数据包将被放行。

- **自动排序（auto）使用“深度优先”的原则进行匹配。**“深度优先”根据ACL规则的精确度排序，如果匹配条件（如协议类型、源和目的IP地址范围等）限制越严格，规则就越先匹配。

> 基本IPv4的ACL的“深度优先”顺序判断原则及步骤如下：
>
> 1. 判断规则中是否带VPN实例，带VPN实例的规则优先。
>
> 2. 比较源IP地址范围，源IP地址范围小（即通配符掩码中“0”位的数量多）的规则优先。例如，“`1.1.1.1 0.0.0.0`”指定了一个IP地址`1.1.1.1`，而“`1.1.1.0 0.0.0.255`”指定了一个网段`1.1.1.1~1.1.1.255`。因前者指定的地址范围比后者小，所以在规则中优先。
>
> 3. 如果源IP地址范围相同，则规则ID(rule-id)小的规则优先。

## ACL分类

根据ACL所具备的特性不同，可将ACL分成不同类型，如：

- 基本ACL
- 高级ACL
- 二层ACL
- 用户自定义ACL

其中应用最广泛的是基本ACL和高级ACL。各种类型ACL区别，如表所示。

| ACL类型    | 编号范围      | 规则制订的主要依据                                 |
| -------- | --------- | ----------------------------------------- |
| 基本ACL    | 2000～2999 | 报文源IP地址等信息。                               |
| 高级ACL    | 3000～3999 | 报文源IP地址、目的IP地址、报文优先级、IP承载的协议类型及特性等三、四层信息。 |
| 二层ACL    | 4000～4999 | 报文的源MAC地址、目的MAC地址、802.1p优先级、链路层协议类型等二层信息  |
| 用户自定义ACL | 5000～5999 | 用户自定义报文的偏移位置和偏移量、从报文中提取出相关内容等信息           |


# 基本ACL和高级ACL

## 基本ACL

基本ACL命令格式

基本ACL**只能基于IP报文**的源IP地址、报文分片标记和时间段信息来定义规则。

配置基本ACL规则命令具有如下结构：
```txt
rule [rule-id] {permit | deny} [source {source-address source-wildcard｜any} fragment | logging | time-range time-name]
```

```txt
规则 规则编号（步长）动作{permit|deny} 源地址 通配符 生效时间
```

对命令中各个组成项的解释如下。

- `rule`表示这是一条规则。
- `rule-id`表示这条规则的编号。
- `permit|deny`是一个“二选一”选项，表示与这条规则相关联的处理动作。用deny命令表示“拒绝”；用permit命令表示“允许”。
- `source`表示源IP地址信息。
- `source-address`表示具体的源IP地址。
- `source-wildcard`表示与source-address相对应的通配符。source-wildcard和source-address结合使用，可以确定出一个IP地址的集合。特殊情况下，该集合中可以只包含一个IP地址。
- `any`表示源IP地址可以是任何地址。
- `fragment`表示该规则只对非首片分片报文有效。
- `logging`表示需要将匹配上该规则的IP报文进行日志记录。
- `time-range time-name`表示该规则的生效时间段为time-name

### 案例1 基本ACL配置

案例背景与要求：某公司网络含外来人员办公区、项目部办工区和财务部办公区。在外来人员办公区中，有一台专供外来人员使用的计算机PC2，IP地址为192.168.2.1/24。出于网络安全考虑，需禁止财务部办公区接收PC2发送的IP报文A。为满足此需求，可在路由器R1上配置基本ACL。基本ACL可根据源IP地址信息识别PC2发送的IP报文A，在GE0/0/3接口的出方向（Outbound方向）上拒绝放行IP报文A。
![500](attachment/Pasted%20image%2020250214104658.png)

配置路由器R1
```txt
[R1]acl 2000          //创建一个编号为2000的基本ACL
[R1-acl-basic-2000]rule deny source 192.168.2.1 0.0.0.0    //在ACL 2000视图下创建如下的规则
[R1-acl-basic-2000]quit
[R1]interface gigabitethernet 0/0/3
[R1-GigabitEthernet0/0/3]traffic-filter outbound acl 2000 //使用报文过滤技术中traffic-filter命令将ACL 2000应用在路由器R1的GE0/0/3接口出方向
```

## 高级ACL

高级ACL可以根据IP报文的源IP地址、目的IP地址、协议字段值、优先级值、长度值，TCP报文的源端口号、目的端口号，UDP报文的源端口号、目的端口号等信息来定义规则。

基本ACL功能只是高级ACL功能的一个子集，高级ACL可比基本ACL定义出更精准、更复杂、更灵活的。

针对所有IP报文简化配置命令格式如下：
```txt
rule [rule-id] {permit|deny} ip [destination {destination-address destination-wildcard | any}] [source {source-address source-wildcard | any}]
```

高级ACL中规则的配置比基本ACL中规则的配置要复杂得多，且配置命令的格式也会因IP报文的载荷数据的类型不同而不同。例如，针对ICMP报文、TCP报文、UDP报文等不同类型的报文，其相应的配置命令的格式也是不同的。

命令相关解释如下：
- destination表示目的IP地址信息。
- destination-address表示具体的目的IP地址。
- destination-wildcard表示与destination-address相对应的通配符。
- destination-wildcard表示与destination-address结合使用，可以确定出一个IP地址的集合。特殊情况下，该集合中可以只包含一个IP地址。
与基本ACL命令格式相同部分的使用方法在这里不做描述。

### 案例1 高级ACL的配置

案例背景与要求：本配置示例网络结构与基本ACL网络结构基本一样，不同的是，要求PC2无法接收来自财务部办公区的IP报文B。此情况下，可在路由器R1上配置高级ACL。高级ACL可根据目的IP地址信息识别去往目的地为外来人员办公区的IP报文B，然后在GE0/0/3接口入方向（Inbound方向）上拒绝放行IP报文B。

![](attachment/Pasted%20image%2020250214105536.png)
配置路由器R1

```txt
[R1]acl 3000    //创建一个编号为2000的基本ACL
[R1-acl-adv-3000]rule deny ip destination 192.168.2.1 0.0.0.0 //在ACL 3000视图下创建如下的规则
[R1-acl-adv-3000]quit
[R1]interface gigabitethernet 0/0/3
[R1-GigabitEthernet0/0/3]traffic-filter inbound acl 3000
//使用报文过滤技术中traffic-filter命令将ACL 3000应用在路由器R1的GE0/0/3接口入方向
```

### 案例2 基本ACL配置实例

案例背景与要求：Jan16公司有网管办公区、市场部办公区、项目部办公区、财务部办公区和服务器区。出于网络安全考虑，希望只有网管办公区PC1才能通过Telnet方式登录到路由器R1上，其他区域PC不能通过Telnet方式登录到路由器R1。
![](attachment/Pasted%20image%2020250214105952.png)
案例配置思路
- 在路由器R1上创建基本ACL。
- 制定基本ACL规则。
- 在路由器的VTY（Virtual Type Terminal）上应用所配置的基本ACL。

配置路由器R1
```txt
<R1>system-view
[R1]acl 2000          //创建一个编号为2000的基本ACL
[R1-acl-basic-2000]rule permit source 192.168.4.1 0        //配置允许规则
[R1-acl-basic-2000]rule deny source any                    //配置拒绝规则
[R1-acl-basic-2000]quit
[R1]user-interface vty 0 4
[R1-ui-vty0-4]acl 2000 inbound              //在VTY接口上应用ACL 2000
```

要在路由器R1上创建ACL，必须首先进入系统视图，然后执行命令acl acl-number。对于基本ACL，acl-number的值必须在2000~2999的范围内，这里确定为2000。

创建了基本ACL 2000后，我们便可以使用rule命令来制定相应的规则。首先，我们制定一条规则：允许源IP地址为192.168.4.1的PC1的IP报文；然后，我们再制定一条规则：拒绝（deny）源IP地址为任意地址的IP报文。

使用 display acl 2000命令来查看ACL 2000匹配信息
```txt
<R1> display acl 2000
Basic ACL 2000, 2 rules
ACL's step is 5
rule 5 permit source 192.168.4.1 0 (0 times matched)
rule 10 deny (0 times matched)
```

从display acl 2000命令的回显信息中我们可以看到，基本ACL 2000中已经存在两条规则，路由器R1为这两条规则自动分配的规则编号分别是5和10。另外，回显信息中的“ACL's step is 5”表示该ACL的规则编号的步长为5，两个“0 times matched”表示该ACL的两条规则的匹配次数都为0（这是因为我们还没有把这个ACL应用到路由器上）。


