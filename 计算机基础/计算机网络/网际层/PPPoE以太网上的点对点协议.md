# 基础命令

**被认证方**

配置本地用户
```txt
aaa 
 local-user 用户名 password cipher 密码
 local-user 用户名 service-type 服务类型 
```

配置虚模板
```txt
ppp authentication-mode 模式{chap}
remote address IP地址
ip address unnmbered 接口
```

进入对应接口
```txt
ip address IP地址
pppoe-server bind Virtual-Template 1 # 关联虚模板到物理接口
```

**认证方**

创建虚拟拨号接口
```
int dial1
 ip address ppp-negotiate
 ppp chap user 用户名
 ppp chap password simple 密码
 dialer user 用户名 # 设置拨号用户
 ppp ipcp default-route
 mtu 1492
```

进入对应接口
```txt
pppoe-client dial-bundle-number 1
```

查看信息

```txt
dis pppoe-client session summary
dis interface dialer 1 # 查看拨号1的信息，如ip地址
```

![](attachment/Pasted%20image%2020231217213733.png)