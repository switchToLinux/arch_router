
我希望使用 Arch Linux配置 软路由系统， 使用 dnsmasq 提供 DHCP 和 DNS服务， 使用 radvd 提供RA服务， 使用 nftables 提供防火墙服务， PPPoE拨号通过 ppp 建立伪接口 ppp0 , 会获取 IPv6-PD ， LAN接口名为 enp0s25 配置静态IPv4地址 192.168.50.1 , IPv6地址需要自动从ppp0获取（::1结尾) ,请按照我的需求提供实现配置。



arch linux 实现单网口软路由需求：

- 单网口 ens0p25 作为wan和lan网口 , 在 ens0p25上建立桥接 br-lan 用于连接lan网口设备。pppoe拨号通过连接:ppp0。
- DNS使用dnsmasq服务，提供 dhcp(v4/v6) 和 dns 服务。
- 支持ipv4 和 ipv6 双栈。
- 防火墙统一使用 nftables 管理。


## TODO
> DIY的软路由系统缺少的主要是UI界面，大部分功能都是通过命令行配置完成的。

- [x] 支持单网口双栈： 单网口 ens0p25 作为wan和lan网口。pppoe拨号通过连接:ppp0。
- [x] 支持DHCP： 使用 dnsmasq 提供 DHCP 和 DNS服务。
- [x] 支持RA： 使用 radvd 提供RA服务， LAN接口名为 enp0s25 配置静态IPv4地址 192.168.50.1, IPv6地址需要自动从ppp0获取（::1结尾)。
- [x] 支持防火墙： 统一使用 nftables 管理。
- [x] 支持IPv6: 支持IPv6双栈，使用 radvd 提供RA服务。
- [ ] 支持WIFI: 桥接 wifi 和 lan 接口，通过 wpa_supplicant 提供 wifi 网络。
- [ ] 支持透明代理： TPROXY透明代理，使用 nftables 实现透明代理。
- [ ] 支持DDNS





## 搭建过程日记

### 第一天：从零开始搭建

所谓的从零开始的基础是已经在 笔记本上安装了 arch linux 系统，并且已经可以正常使用桌面系统了，使用了 Hyprland 桌面环境。

由于没有从零搭建软路由的经验，搭建之前收集资料工作是必不可少的，但是在网上没有什么经验可以借鉴，只能自己摸索。


Arch Linux的wiki文档有[双网口软路由的搭建过程资料](https://wiki.archlinux.org/title/Router)，虽然不是特别详细，但提供了基本的搭建流程，那就参考这个搭建流程就可以了。





## 功能说明


- ~~dhcpcd~~: DHCP客户端，可帮助网卡配置公网IPv6地址，非必要，如果使用 Systemd-Networkd服务可以不必使用这个服务。
- radvd ： 为局域网内设备配置IPv6地址。
- nftables ： 配置防火墙规则。
- dnsmasq ： 为内网提供 DHCP服务和DNS服务。



## 关于IPv6地址的配置

| 接口                 | 功能                                                       |
| ------------------ | -------------------------------------------------------- |
| `ppp0`             | 由 `ppp` 创建，自动获取 IPv6 地址 + IPv6-PD（通过 IPCPv6）             |
| `enp0s25`          | 通过脚本，从 `ppp0` 获取的 PD 自动配置 `2408:xxxx:xxxx:xxxx::1/64` 地址 |
| `radvd`            | 向 LAN 广播 `2408:xxxx:xxxx:xxxx::/64` 前缀，实现 SLAAC          |
| `systemd-networkd` | 仅负责静态设置 enp0s25 的 IPv4 地址等，不负责 IPv6 自动获取                 |
