
基础配置命令
```eNSP
wlan
	ap auth-mode mac-auth # mac认证模式
	ap-id 1 ap-mac mac地址
	ap-name ap名称
capwap source interface vlanid


# 创建ssid模板
wlan
	ssid-profile name 模板名称
	ssid Wlan名称

# 连接认证方式
wlan
	security-profile name 认证名称
	security 认证类型 加密方式 pass-phease 密码
	# open 开发连接，不需要认证，通常使用在guest模式

# 设置业务vlan
wlan
	vap-profile name vap名称
	ssid-profile 模板名称
	security-profile 认证名称
	service-vlan vlan-id vlanid # 设置用户所在vlan

# 设置转发方式
wlan
	vap-profile name vap名称
	forward-mode direct-forwad 
	# direct-forwad：直接转发（系统默认），softgre：软gre，tunnel:隧道
	
# 应用vap到ap（下发）
wlan
	ap-id 1
		vap-profile vap名称 wlan 1 radio 0 # 2.4G频段
		vap-profile vap名称 waln 1 radio 1 # 5G频段

# 查看相关信息
dis capwap configuration
dis vap-profile name vap名称
dis station all
dis vlan summary
```


# 配置案例

## 旁挂二层组网
![](attachment/Pasted%20image%2020231124104224.png)

配置路由器AR1
```txt
#
 sysname R1
#
vlan batch 7 10
#
interface GigabitEthernet0/0/0
 ip address 10.1.17.1 255.255.255.0 
#
interface LoopBack0
 ip address 10.1.1.1 255.255.255.255 
#
```

配置交换机S1
```txt
#
sysname S1
#
vlan batch 10 17 254
#
dhcp enable
#
interface Vlanif10
 ip address 10.1.10.7 255.255.255.0
 ospf enable 1 area 0.0.0.0
 dhcp select interface
#
interface Vlanif17
 ip address 10.1.17.7 255.255.255.0
 ospf enable 1 area 0.0.0.0
#
interface Vlanif254
 ip address 10.1.254.7 255.255.255.0
 dhcp select interface
#
interface GigabitEthernet0/0/1
 port link-type trunk
 port trunk pvid vlan 17
 port trunk allow-pass vlan 2 to 4094
#
interface GigabitEthernet0/0/2
 port link-type trunk
 port trunk pvid vlan 254
 port trunk allow-pass vlan 2 to 254
#
interface GigabitEthernet0/0/3
 port link-type trunk
 port trunk pvid vlan 254
 port trunk allow-pass vlan 2 to 4094
#
ospf 1
 area 0.0.0.0
  network 10.1.17.0 0.0.0.255
  network 10.1.10.0 0.0.0.255
  network 10.1.254.0 0.0.0.255
#
```

配置AC
```txt
#
 sysname AC
#
vlan batch 254
#
dhcp enable
#
interface Vlanif254
 ip address 10.1.254.10 255.255.255.0
 dhcp select interface
#
interface GigabitEthernet0/0/1
 port link-type trunk
 port trunk pvid vlan 254
 port trunk allow-pass vlan 2 to 4094
#
wlan
 security-profile name SSID_3306
  security wpa2 psk pass-phrase %^%#[W*`X|5JR6;%#~;&kZ#~K{*H93$CQ'\1d="kL+r4%^%#
 aes
 ssid-profile name SSID_3306
  ssid HUAWEI 2201
 vap-profile name VAP_3306
  service-vlan vlan-id 10
  ssid-profile SSID_3306
  security-profile SSID_3306
 ap-group name default
  radio 0
   vap-profile VAP_3306 wlan 1
  radio 1
   vap-profile VAP_3306 wlan 1
#

```

配置AP
```eNSP
vlan batch 10
```