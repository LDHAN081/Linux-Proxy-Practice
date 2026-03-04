# Linux-Proxy-Practice
# 任务 1：搭建 Ubuntu 虚拟机与 Apache Web 服务器

## 1. 虚拟机 (VM) 准备
* **软件**：VMware Workstation
* **镜像**：Ubuntu ISO 文件
* **配置要点**：
  * **网络适配器**：必须选 **NAT 模式**（确保与 Windows 宿主机互通）。
  * **账号设置**：创建用户（例如 `ldhan`），设置密码。

## 2. 安装核心服务 (SSH & Apache)
等待 Ubuntu 自动安装完成并进入桌面后，右键打开终端（Terminal），执行以下命令：

```bash
# 更新软件源，并一键安装 Apache (网页服务) 和 OpenSSH (远程连接)
sudo apt update && sudo apt install apache2 openssh-server -y

# 设置开机自启并立即启动这两个服务
sudo systemctl enable --now apache2 ssh

# 允许 SSH (22端口) 连接
sudo ufw allow ssh

# 允许 HTTP (80端口) 网页连接
sudo ufw allow 80/tcp

# 激活防火墙 (提示中断连接时按 y 确认)
sudo ufw enable

# 查看防火墙状态，确认规则已生效
sudo ufw status
