# Arch软路由实现方案

> 本方案适用于单网口实现软路由，至于局域网的主机链路可以通过 光猫（关闭DHCP功能）或者交换机连接。关闭光猫的DHCP服务后局域网内设备才能使用 Arch软路由的DHCP服务。

软路由的实现功能目标：
* PPPoE服务： 使用软件 `ppp`, 通过 pppoe 拨号上网，获取 IPv4 和 IPv6 地址
* DNS服务： 使用软件 `dnsmasq` 实现 DNS 服务，提供域名解析服务和 RA 服务。
* DHCPv4/v6服务： 使用软件 `dhcpcd` 实现 DHCPv4/v6 服务，分配 IPv4/IPv6 地址。
* ~~RA服务~~： （弃用）使用软件 `radvd` 实现 Router Advertisement（RA）服务，通告 IPv6 前缀和默认网关。

> Dnsmasq 的DHCP服务不能自动配置 PPPoE 拨号获得IPv6地址，只能用 `dhcpcd` 实现 DHCPv6 服务自动为 ppp0和 enp0s25网络配置IPv6地址。

## Arch软路由的网络拓扑图



```plaintext


                         ┌────────────────────────────┐
                         │      Internet (ISP)        │
                         │ IPv4 + IPv6 + PD (/64)     │
                         └────────────┬───────────────┘
                                      │
                                      │ PPPoE (ppp0) （WAN口）
                                      │
                            ┌─────────▼──────────┐
                            │   Arch 软路由       │
                            │  Arch Linux + PPP  │
                            │                    │
                            │ IPv4: IPv4(PPPoE)  │
                            │ IPv6: 2408:xxxx::/64 ←─┐
                            │ PD:   2408:yyyy::/64   │
                            │                        │
                            └─────────┬──────────────┘
                                      │
                         静态 IPv4: 192.168.50.1/24
                         SLAAC 或 DHCPv6: 2408:yyyy::/64
                                      │ 交换机/(关闭DHCP的光猫) 内联网口
                           ┌──────────▼──────────┐
                           │      交换机/光猫     │
                           └──────────┬──────────┘
                                      │
              ┌───────────────────────┼──────────────────────┐
              │                       │                      │
       ┌──────▼──────┐         ┌──────▼──────┐        ┌──────▼──────┐
       │ 内主机 A     │         │ 内主机 B     │        │ 内主机 C     │
       │ (手机/PC等)  │         │ (电视/IoT等) │        │ (NAS/打印机) │
       └─────────────┘         └─────────────┘        └─────────────┘

       IPv4: 192.168.50.x          IPv6: 自动获得 2408:yyyy::xxxx

```
> 软件依赖: ppp, dnsmasq, dhcpcd, nftables

问题总结：
- dhcpcd 提供 DHCPv6服务时，会和dnsmasq的RA服务冲突，导致RA服务失效，导致LAN内主机无法获取到IPv6地址。 解决方法：使用 radvd提供RA服务，然后手动配置 enp0s25 网络的 IPv6 地址(而不使用dhcpcd服务自动配置)。


架构说明

| 部分          | 描述                                           |
| ----------- | -------------------------------------------- |
| **ppp0**    | Arch 使用 PPPoE 拨号连接 ISP，获取公网 IPv4 和 IPv6      |
| **IPv6 PD** | ISP 分配一个 /64（或更大）前缀供你下发给 LAN                 |
| **dhcpcd**  | 从 `ppp0` 获取 prefix，并将其分配给 `enp0s25`          |
| **内网设备**    | 能够自动获得 IPv6 地址并直接访问外网，无需 NAT                 |



基于你对 **OpenWrt IPv6 实现方案的熟悉**，我可以为你的 **Arch Linux 软路由系统**提供一个**尽可能等效的 IPv6 自动化实现方案**，包括：

* **WAN口通过 PPPoE 获取 IPv6 和 Prefix Delegation（PD）**
* **LAN 口自动分发 IPv6（通过 RA 或 DHCPv6）**
* **实现 IPv6 转发、正确的路由广播与 NAT-less 出网**


## ✅ 实现结果（对标 OpenWrt）

| 功能          | OpenWrt | Arch 软路由         |
| ----------- | ------- | -------------- |
| IPv6-PD 获取  | odhcp6c | dhcpcd         |
| RA 广播       | odhcpd  | radvd          |
| Forwarding  | 默认启用    | sysctl 启用      |
| IPv6 路由     | 自动      | dhcpcd + radvd |
| 无NAT IPv6访问 | 支持      | 支持             |

---

## 🧠 补充建议（高级）

* 如果你希望避免内核自动添加不希望的 IPv6 路由：设置 `accept_ra = 0`
* 如果你希望让 LAN 设备也支持 DHCPv6（非 SLAAC）：可以使用 `dhcp6s`（较复杂，不推荐）
* 你可以完全用 `systemd-networkd` 管理网络，稍微复杂，但也干净灵活（我可提供示例）

---

是否需要我提供完整的 `/etc/dhcpcd.conf` 和 `/etc/radvd.conf` 样板，以及自动启动脚本或诊断工具脚本？你只需部署即可。欢迎继续提问！


## 问题记录

### 是否可以通过 dnsmasq 替换 radvd 服务呢？


- 方案一： dnsmasq(DNS和DHCPv4) + radvd(RA服务) + ppp(PPPoE拨号调用脚本执行设置IPv6地址脚本)。
- 方案二： dnsmasq(DNS和DHCPv4) + dhcpcd(RA服务)

