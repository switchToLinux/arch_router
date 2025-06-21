# Arch软路由FAQ

这里记录一些Arch软路由配置过程中遇到的问题记录。


## IPv6相关的问题

### dhcpcd自动分配IPv6地址一段时间后 enp0s25接口的IPv6地址没有了？

最开始配置dhcpcd服务自动获取IPv6地址和IPv6-PD前缀分配给 enp0s25 接口，一段时间后 dhcpcd服务日志显示下面删除 enp0s25 接口的IPv6地址和前缀，但是 enp0s25 接口的IPv6地址还是可以 ping 通的。

```
6月 18 14:47:34 mybook dhcpcd[1213]: enp0s25: pid 1 deleted route to 2408:xxxx:c16:20c5::/64
6月 18 14:47:34 mybook dhcpcd[1213]: enp0s25: pid 0 deleted address 2408:xxxx:c16:20c5::1/64

```

为什么 dhcpcd 服务删除 enp0s25 接口的 IPv6 地址和前缀？ 

根据我的实际尝试发现，这是因为 `pppd` 服务拨号成功后会执行 `/etc/ppp/ipv6-up.d/00-iface-config.sh` 脚本，内容如下：
```bash
#!/bin/sh

echo 0 > /proc/sys/net/ipv6/conf/$1/use_tempaddr
echo 2 > /proc/sys/net/ipv6/conf/$1/accept_ra

```

解决办法：

1. 删除 `/etc/ppp/ipv6-up.d/00-iface-config.sh` 脚本。
2. 注释掉这两行配置命令。



## 最后



