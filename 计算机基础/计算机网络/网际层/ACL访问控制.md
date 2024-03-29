
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
> 例如，将规则编号的步长设定为10（注：规则编号的步长的默认值为5），则规则编号将按照10、20、30、40 …这样的规律自动进行分配。如果将规则编号的步长设定为2，则规则编号将按照2、4、6、8 …这样的规律自动进行分配。步长的大小反映了相邻规则编号之间的间隔大小。
> 
> 间隔的存在，实际上是为了方便在两个相邻的规则之间插入新的规则。

- 默认情况下，ACL中的所有规则均按照规则ID从小到大的顺序与规则进行匹配

 - 规则ID之间会留下一定的间隔。如果不指定规则ID时，具体间隔大小由“ACL的步长”来设定。



## ACL规则匹配

1. 配置了ACL的设备在接收到一个报文之后，会将该报文与ACL中的规则逐条进行匹配

2. 如果不能匹配上当前这条规则，则会继续尝试去匹配下一条规则

3. 一旦报文匹配上了某条规则，则会对该报文执行这条规则中定义的处理动作（permit或deny），并且不再继续尝试与后续规则进行匹配

4. 如果报文不能匹配上ACL的任何一条规则，则设备会对该报文执行“permit”这个处理动作。

在将一个数据包和访问控制列表的规则进行匹配的时候，由规则的匹配顺序决定规则的优先级。华为设备支持以下两种匹配顺序。

**匹配顺序按照用户配置ACL规则的先后序列进行匹配，先配置的规则先匹配。** 根据ACL中语句的顺序，把数据包和判断条件进行比较。一旦匹配，就采用语句中的动作并结束比较过程，不再检查以后的其他条件判断语句。如果没有任何语句匹配，数据包将被放行。

**自动排序（auto）使用“深度优先”的原则进行匹配。**“深度优先”根据ACL规则的精确度排序，如果匹配条件（如协议类型、源和目的IP地址范围等）限制越严格，规则就越先匹配。

基本IPv4的ACL的“深度优先”顺序判断原则及步骤如下：

- 判断规则中是否带VPN实例，带VPN实例的规则优先。

- 比较源IP地址范围，源IP地址范围小（即通配符掩码中“0”位的数量多）的规则优先。例如，“`1.1.1.1 0.0.0.0`”指定了一个IP地址`1.1.1.1`，而“`1.1.1.0 0.0.0.255`”指定了一个网段`1.1.1.1~1.1.1.255`。因前者指定的地址范围比后者小，所以在规则中优先。

- 如果源IP地址范围相同，则规则ID(rule-id)小的规则优先。

## ACL分类

根据ACL所具备的特性不同，可将ACL分成不同类型，如：

- 基本ACL
- 高级ACL
- 二层ACL
- 用户自定义ACL

其中应用最广泛的是基本ACL和高级ACL。各种类型ACL区别，如表所示。

| ACL类型       | 编号范围   | 规则制订的主要依据                                                           |
| ------------- | ---------- | ---------------------------------------------------------------------------- |
| 基本ACL       | 2000～2999 | 报文源IP地址等信息。                                                         |
| 高级ACL       | 3000～3999 | 报文源IP地址、目的IP地址、报文优先级、IP承载的协议类型及特性等三、四层信息。 |
| 二层ACL       | 4000～4999 | 报文的源MAC地址、目的MAC地址、802.1p优先级、链路层协议类型等二层信息         |
| 用户自定义ACL | 5000～5999 | 用户自定义报文的偏移位置和偏移量、从报文中提取出相关内容等信息                                                                             |


# 基本ACL和高级ACL

## 基本ACL

基本ACL命令格式

基本ACL只能基于IP报文的源IP地址、报文分片标记和时间段信息来定义规则。

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
- `source- wildcard`表示与source-address相对应的通配符。source-wildcard和source-address结合使用，可以确定出一个IP地址的集合。特殊情况下，该集合中可以只包含一个IP地址。
- `any`表示源IP地址可以是任何地址。
- `fragment`表示该规则只对非首片分片报文有效。
- `logging`表示需要将匹配上该规则的IP报文进行日志记录。
- `time-range time-name`表示该规则的生效时间段为time-name

## 高级ACL
