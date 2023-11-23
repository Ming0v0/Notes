- 将多个三层设备的接口加入到一个VRRP备份组中用虚拟IP地址代表这一组网关
- 仅由master接口承担网关的转发功能当master接口出现故障时，会在backup接口中重新选取1个作为master接口
- 只要VRRP备份组中仍有1个接口正常工作，网络对外通信就不会中断。
- VRRP优先级最高的成为master接口 (优先级相同时，IP地址大的成为master)

```txt
<Huawei>system-view
[Huawei]interface Ethernet0/0/1
[Huawei-Ethernet0/0/1]ip addres ip地址 子网掩码
[Huawei]interface Ethernet0/0/2
[Huawei-Ethernet0/0/2]ip addres ip地址 子网掩码 配置连接主机的接口IP地址
[Huawei-Ethernet0/0/2]vrrp vrid 1 virtual-ip ip地址 配置备份组1的虚拟网关地址
[Huawei-Ethernet0/0/2]vrrp vrid 1 priority 优先级 配置路由器在备份组1中的优先级
[Huawei-Ethernet0/0/2]vrrp vrid 2 virtual-ip ip地址 配置备份组2的虚拟网关地址
[Huawei]display vrrp 查看路由器的VRRP状态
```