
基础配置命令
```eNSP
wlan
	ap auth-mode mac-auth # mac认证模式
	ap-id 1 ap-mac macAdress # ap的mac地址
	ap-name APName # AP的名称
capwap source interface vlanId


（1）创建ssid模板
wlan
	ssid-profile name 模板名称
	ssid Wlan名称

（2）设置连接认证方式
wlan
	security-profile name 认证名称
	security 认证类型 加密方式 pass-phease 密码
	# open 开放连接，不需要认证，通常使用在guest模式

（3）设置业务vlan
wlan
	vap-profile name vap名称
	ssid-profile 模板名称
	security-profile 认证名称
	service-vlan vlan-id vlanid # 设置用户所在vlan

（4）设置转发方式
wlan
	vap-profile name vap名称
	forward-mode direct-forwad 
	# direct-forwad：直接转发（系统默认），softgre：软gre，tunnel:隧道
	
（5）应用vap到ap（下发）
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

配置路由器AR
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

配置交换机SW

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
  security wpa2 psk pass-phrase Huawei@123 aes
 ssid-profile name SSID_3306
  ssid HUAWEI 2201
 vap-profile name VAP_3306
  service-vlan vlan-id 10
  ssid-profile SSID_3306
  security-profile SSID_3306
 ap-id 1 
   vap-profile VAP_3306 wlan 1 radio 0
   vap-profile VAP_3306 wlan 1 radio 1
#
```

配置AP

```eNSP
vlan batch 10
```


## 案例1：旁挂三层组网

![](attachment/Pasted%20image%2020231124132317.png)

配置AR
```txt
#
 sysname R2
#
interface GigabitEthernet0/0/0
 ip address 10.1.17.1 255.255.255.0 
#
interface LoopBack0
 ip address 10.1.1.1 255.255.255.255 
#
ospf 1 
 area 0.0.0.0 
  network 10.1.1.1 0.0.0.0 
  network 10.1.17.0 0.0.0.255 
#
```

配置SW
```txt
#
sysname S2
#
undo info-center enable
#
vlan batch 7 17 30 40 254
#
dhcp enable
#
interface Vlanif7
 ip address 10.1.7.7 255.255.255.0
 ospf enable 1 area 0.0.0.0
#
interface Vlanif17
 ip address 10.1.17.7 255.255.255.0
 ospf enable 1 area 0.0.0.0
#
interface Vlanif30
 ip address 10.1.30.7 255.255.255.0
 dhcp select interface
 dhcp server option 43 sub-option 2 ip-address 10.1.7.10
#
interface Vlanif40
 ip address 10.1.40.7 255.255.255.0
 dhcp select interface
 dhcp server option 43 sub-option 2 ip-address 10.1.7.10
#
interface Vlanif254
 ip address 10.1.254.7 255.255.255.0
 ospf enable 1 area 0.0.0.0
 dhcp select interface
 dhcp server option 43 sub-option 2 ip-address 10.1.7.10
#
interface GigabitEthernet0/0/1
 port link-type trunk
 port trunk pvid vlan 17
 port trunk allow-pass vlan 2 to 4094
#
interface GigabitEthernet0/0/2
 port link-type trunk
 port trunk pvid vlan 7
 port trunk allow-pass vlan 2 to 4094
#
interface GigabitEthernet0/0/3
 port link-type trunk
 port trunk pvid vlan 254
 port trunk allow-pass vlan 7 17 30 254
#
interface GigabitEthernet0/0/4
 port link-type trunk
 port trunk pvid vlan 254
 port trunk allow-pass vlan 7 17 40 254
#
ospf 1
 area 0.0.0.0
  network 10.1.30.0 0.0.0.255
  network 10.1.40.0 0.0.0.255
  network 10.1.17.0 0.0.0.255
#
```

配置AC
```txt
#
 sysname AC2
#
vlan batch 7 30 40 254
#
interface Vlanif7
 ip address 10.1.7.10 255.255.255.0
#
interface GigabitEthernet0/0/1
 port link-type trunk
 port trunk pvid vlan 7
 port trunk allow-pass vlan 2 to 4094
#
ip route-static 0.0.0.0 0.0.0.0 10.1.7.7
#
capwap source interface vlanif7
#
wlan
 security-profile name SEC-2303
  security wpa2 psk pass-phrase Huawei@123 aes
 ssid-profile name SSID-2303
  ssid HUAWEI-2023
 vap-profile name VAP-2303
  service-vlan vlan-id 30
  ssid-profile SSID-2303
  security-profile SEC-2303
 vap-profile name VAP-2304
  service-vlan vlan-id 40
  ssid-profile SSID-2303
  security-profile SEC-2303
 ap-id 1 
   vap-profile VAP-2303 wlan 1 radio 0
   vap-profile VAP-2303 wlan 1 radio 1
 ap-id 2
  vap-profile VAP-2304 wlan 1 radio 0
  vap-profile VAP-2304 wlan 1 radio 1
```

配置AP2
```txt
vlan batch 30
```

配置AP3
```txt
vlan batch 40
```


## 案例2：旁挂三层组网
![](attachment/Pasted%20image%2020231126121847.png)

配置AR
```txt
#
 sysname R1
#
 undo info-center enable
#
interface GigabitEthernet0/0/0
 ip address 10.1.17.1 255.255.255.0 
#
interface LoopBack0
 ip address 10.1.1.1 255.255.255.255 
#
ospf 1 
 area 0.0.0.0 
  network 10.1.1.1 0.0.0.0 
  network 10.1.17.0 0.0.0.255 
#
```

配置SW
```txt
#
sysname S1
#
undo info-center enable
#
vlan batch 7 17 30 40 254
#
dhcp enable
#
interface Vlanif1
#
interface Vlanif7
 ip address 10.1.7.7 255.255.255.0
 ospf enable 1 area 0.0.0.0
#
interface Vlanif17
 ip address 10.1.17.7 255.255.255.0
 ospf enable 1 area 0.0.0.0
#
interface Vlanif30
 ip address 10.1.30.7 255.255.255.0
 dhcp select interface
#
interface Vlanif40
 ip address 10.1.40.7 255.255.255.0
 dhcp select interface
#
interface Vlanif254
 ip address 10.1.254.7 255.255.255.0
 ospf enable 1 area 0.0.0.0
 dhcp select interface
 dhcp server option 43 sub-option 2 ip-address 10.1.7.10
#
interface GigabitEthernet0/0/1
 port link-type trunk
 port trunk pvid vlan 17
 port trunk allow-pass vlan 2 to 4094
#
interface GigabitEthernet0/0/2
 port link-type trunk
 port trunk pvid vlan 7
 port trunk allow-pass vlan 2 to 4094
#
interface GigabitEthernet0/0/3
 port link-type trunk
 port trunk pvid vlan 254
 port trunk allow-pass vlan 2 to 4094
#
ospf 1
 area 0.0.0.0
  network 10.1.17.0 0.0.0.255
  network 10.1.30.0 0.0.0.255
  network 10.1.40.0 0.0.0.255
#
```

配置AC
```txt
#
 sysname AC
#
vlan batch 7 17 30 40 254
#
interface Vlanif7
 ip address 10.1.7.10 255.255.255.0
#
interface Vlanif254
#
interface GigabitEthernet0/0/1
 port link-type trunk
 port trunk pvid vlan 7
 port trunk allow-pass vlan 2 to 4094
#
ip route-static 0.0.0.0 0.0.0.0 10.1.7.7
#
capwap source interface vlanif7
#
wlan
 security-profile name SEC-OPEN
 security-profile name SEC-GXNZD
  security wpa2 psk pass-phrase Huawei@123 aes
 ssid-profile name GXNZD
  ssid GXNZD
 ssid-profile name GXNZD-open
  ssid GXNZD-open
 vap-profile name VAP-guest
  forward-mode tunnel
  service-vlan vlan-id 40
  ssid-profile GXNZD-open
  security-profile SEC-OPEN
 vap-profile name VAP-gxnzd2303
  forward-mode tunnel
  service-vlan vlan-id 30
  ssid-profile GXNZD
  security-profile SEC-GXNZD
 regulatory-domain-profile name DOMAIN-2303
 ap-group name GROUP-1
  regulatory-domain-profile DOMAIN-2303
  radio 0
   vap-profile VAP-gxnzd2303 wlan 1
  radio 1
   vap-profile VAP-gxnzd2303 wlan 1
 ap-group name GROUP-2
  regulatory-domain-profile DOMAIN-2303
  radio 0
   vap-profile VAP-guest wlan 1
  radio 1
   vap-profile VAP-guest wlan 1
 ap-id 1 type-id 69 ap-mac 00e0-fc13-25e0 ap-sn 210235448310B95E0E3D
  ap-name area-1
  ap-group GROUP-1
 ap-id 2 type-id 69 ap-mac 00e0-fc28-2060 ap-sn 210235448310FB51B011
  ap-name area-2
  ap-group GROUP-1
 ap-id 3 type-id 69 ap-mac 00e0-fc8a-14c0 ap-sn 210235448310A9523021
  ap-name area-3
  ap-group GROUP-1
 ap-id 4 type-id 69 ap-mac 00e0-fc80-3630 ap-sn 210235448310F05F2809
  ap-name area-4
  ap-group GROUP-2
#
```