# 基础命令

**被认证方**

配置本地用户
```txt
aaa 
 local-user 用户名 password 加密方式[cipher/simple] 密码
 local-user 用户名 service-type 服务类型[ppp] 
```

配置虚模板
```txt
int Virtual-Template num
 ppp authentication-mode 模式[chap]
 remote address IP地址
 ip address unnmbered 接口
```

进入对应接口
```txt
ip address IP地址
pppoe-server bind Virtual-Template num # 关联虚模板到物理接口
```

**认证方**

创建虚拟拨号接口
```
int dial1
 ip address ppp-negotiate
 ppp chap user 用户名
 ppp chap password 加密方式[cipher/simple] 密码
 dialer user 用户名 # 设置拨号用户
 dialer bundle num # 关联ID
 ppp ipcp default-route # 设置缺省路由
 mtu 1492
```

进入对应接口
```txt
pppoe-client dial-bundle-number 1
```

查看信息

```txt
dis pppoe-server session all

dis pppoe-client session summary
dis interface dialer 1 # 查看拨号1的信息，如ip地址
```

![](attachment/Pasted%20image%2020231217213733.png)