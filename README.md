# Arch router

Arch Linux 软路由工具箱!

- [x] ainstall: arch linux 安装脚本,分区采用GPT分区表 + UEFI(bootloader) + LVM逻辑卷管理方式，更容易扩容。
- [x] arouter : arch软路由工具箱，安装配置软路由 (默认支持IPv4/IPv6双协议栈)，不断扩展管理软路由服务等功能。


## 使用方法

<details open>
  <summary>下载 ainstall 脚本安装Arch Linux</summary>

  ```bash

  # curl -L https://raw.githubusercontent.com/switchToLinux/arch_router/refs/heads/main/scripts/ainstall -o ainstall
  curl -L https://pushx.link/s/ainstall -o ainstall

  chmod +x ainstall && sudo mv ainstall /usr/local/bin

  sudo ainstall
  ```

</details>


<details>
  <summary>下载 arouter 脚本安装配置Arch软路由</summary>

  ```bash
  #curl -L https://raw.githubusercontent.com/switchToLinux/arch_router/refs/heads/main/scripts/arouter -o arouter
  curl -L https://pushx.link/s/arouter -o arouter

  chmod +x arouter && sudo mv arouter /usr/local/bin

  sudo arouter
  ```
</details>

## 功能说明


### ainstall功能

`ainstall`脚本用于安装 Arch Linux系统，提供的功能如下：

| 功能 | 内容 |
| ---- | -----|
| 磁盘分区| GPT分区表/UEFI/LVM分区方式 |
| 基础安装| 最小安装(不带UI界面)：base linux-lts linux-firmware linux-lts-headers vim sudo shadow lvm2 zsh networkmanager openssh  man-db man-pages texinfo  intel-ucode sof-firmware binutils git curl wget fzf plocate |
| 系统设置| root设置/新用户设置/时区设置上海/locale支持zh_CN.UTF-8 |


### arouter功能

`arouter`脚本用于安装配置单网口 Arch软路由，提供的功能如下：
| 功能 | 内容 |
| ---- | -----|
| 基础包安装| ppp dnsmasq nftables dhcpcd chrony|
| 网络配置| 使用 systemd-networkd 配置LAN网络信息（设置IP地址)|
| PPPoE设置| 使用 ppp 配置PPPoE拨号用户信息|
| 配置Dnsmasq | 使用 Dnsmasq 提供内网DHCPv4和RA广播服务|
| 防火墙配置 | 使用 nftables 配置防火墙规则 |
| 系统内核配置 | 配置 sysctl 参数，开启转发功能，优化软路由内核参数|
| dhcpcd配置 | 使用 dhcpcd 提供IPv6支持(获取IPv6-PD和LAN接口的IPv6地址管理) |



## 最后

为什么有这个项目？

习惯使用命令行操作，习惯了自己定制适合自己的系统。

同时，为了不再每次都要重复输入各种指令，于是我编写了这个脚本工具。

通过脚本快速搭建相同系统环境，并且减少配置错误的情况。

## 贡献

欢迎提交PR，或在issue中提出建议。

