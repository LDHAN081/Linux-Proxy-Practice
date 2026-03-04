# Linux-Proxy-Practice
# 任务 1：VMware 虚拟机安装与 Ubuntu Web 服务器搭建详细手顺

## 1. 软件与镜像下载
* **VMware Workstation**: 
  * 现已并入 Broadcom，个人使用免费。去 Broadcom 支持中心注册账号并下载 Workstation Pro。
  * 官方地址：`https://support.broadcom.com/` (搜索 VMware Workstation Pro)
* **Ubuntu 系统镜像 (ISO)**: 
  * 推荐下载 LTS（长期支持版，如 22.04 或 24.04）的 Desktop 桌面版镜像。
  * 官方下载：`https://ubuntu.com/download/desktop`

## 2. 在 VMware 中创建虚拟机
1. 打开 VMware，点击 **Create a New Virtual Machine** (新建虚拟机)。
2. 选择 **Typical** (典型)，点击 Next。
3. 选择 **Installer disc image file (iso)**，点击 Browse 选择你刚下载的 Ubuntu ISO 文件。
4. 填写简易安装信息：
   * Full name: 随便填
   * User name: `user` (你的登录账户名)
   * Password: `123456` (你的密码)
5. 命名与存放：
   * 名字填 `Ubuntu1`。
   * 位置选一个空间充足的盘（千万别直接丢根目录，建议建个文件夹如 `E:\VM\Ubuntu1`）。
6. 磁盘设置：大小保持默认 20GB，选中 **Split virtual disk into multiple files**。
7. **网络关键设置**：点击 **Customize Hardware** (自定义硬件)，找到 **Network Adapter** (网络适配器)，**必须选中 NAT mode**（这样才能和 Windows 宿主机互通）。
8. 点击 Finish，等待系统自动安装完成并进入桌面。

## 3. 安装核心服务
等 Ubuntu 安装好看到紫色桌面后，右键打开终端 (Terminal)，逐行执行以下命令：

```bash
# 1. 更新系统的软件源列表
sudo apt update

# 2. 安装 Apache 网页服务 (-y 表示免确认直接安装)
sudo apt install apache2 -y

# 3. 安装 SSH 远程连接服务
sudo apt install openssh-server -y
## 6. 验证与成果确认

这是最后一步，用来证明你的服务器已经成功跑起来了。

### 1. 获取 Ubuntu 本机 IP
在 Ubuntu 虚拟机的终端里输入查询命令：
```bash
ip a

ssh ldhan@192.168.147.128
