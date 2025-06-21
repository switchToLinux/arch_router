# Arch router

Arch Linux 软路由工具箱!

- [x] ainstall: arch linux 安装脚本。
- [x] arouter : arch软路由工具箱


## 使用方法

<details open>
  <summary>下载 ainstall 脚本安装Arch Linux</summary>

  ```bash

  curl -L https://pushx.link/s/ainstall -o ainstall
  curl -L https://raw.githubusercontent.com/switchToLinux/arch_router/refs/heads/main/scripts/ainstall -o ainstall

  chmod +x ainstall && sudo mv ainstall /usr/local/bin

  sudo ainstall
  ```
</details>


<details>
  <summary>下载 arouter 脚本安装配置Arch软路由</summary>

  ```bash
  curl -L https://pushx.link/s/arouter -o arouter
  curl -L https://raw.githubusercontent.com/switchToLinux/arch_router/refs/heads/main/scripts/arouter -o arouter

  chmod +x arouter && sudo mv arouter /usr/local/bin

  sudo arouter
  ```
</details>


## 最后

为什么有这个项目？

习惯使用命令行操作，习惯了自己定制适合自己的系统。

同时，为了不再每次都要重复输入各种指令，于是我编写了这个脚本工具。

通过脚本快速搭建相同系统环境，并且减少配置错误的情况。

## 贡献

欢迎提交PR，或在issue中提出建议。

