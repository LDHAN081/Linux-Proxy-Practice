# Linux-Proxy-Practice

## 任务 1：VMware 虚拟机安装与 Ubuntu Web 服务器搭建详细手顺

### 1. 软件与镜像下载
* **VMware Workstation**: 
  * 现已并入 Broadcom，个人使用免费。去 Broadcom 支持中心注册账号并下载 Workstation Pro。
  * 官方地址：`https://support.broadcom.com/` (搜索 VMware Workstation Pro)
* **Ubuntu 系统镜像 (ISO)**: 
  * 推荐下载 LTS（长期支持版，如 22.04 或 24.04）的 Desktop 桌面版镜像。
  * 官方下载：`https://ubuntu.com/download/desktop`

### 2. 在 VMware 中创建虚拟机
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

### 3. 安装核心服务
等 Ubuntu 安装好看到紫色桌面后，右键打开终端 (Terminal)，逐行执行以下命令：

```bash
# 1. 更新系统的软件源列表
sudo apt update

# 2. 安装 Apache 网页服务 (-y 表示免确认直接安装)
sudo apt install apache2 -y

# 3. 安装 SSH 远程连接服务
sudo apt install openssh-server -y
```

### 4. 验证与成果确认
这是最后一步，用来证明你的服务器已经成功跑起来了。

#### 1. 获取 Ubuntu 本机 IP
在 Ubuntu 虚拟机的终端里输入查询命令：
```bash
ip a
```
> **提示**：在输出结果中找到 `ens33` 这一段，记下 `inet` 后面的 IP 地址（例如：`192.168.xxx.xxx`），后面都要用到它。

#### 2. 测试 SSH 远程连接
1. 回到你的 Windows 电脑，按 `Win + R` 键，输入 `cmd` 并回车，打开命令行窗口。
2. 输入以下连接命令（请把 IP 换成你刚才实际查到的）：
```bash
ssh user@192.168.xxx.xxx
```
> **提示**：第一次连接会询问是否信任主机，输入 `yes` 回车。接着输入密码（Linux 输入密码时屏幕不显示字符，盲输完直接回车即可）。只要命令提示符变成类似 `user@ubuntu:~$`，就说明你成功从 Windows 连上虚拟机了。

#### 3. 测试 Web 网页 (成果物截图)
1. 打开 Windows 上的浏览器（Chrome 或 Edge）。
2. 在顶部地址栏直接输入你的虚拟机 IP：`http://192.168.xxx.xxx`
3. 回车访问。如果你看到一个写着 **"Apache2 Ubuntu Default Page"** 的官方欢迎网页，立刻截图保存。

🎉 **恭喜！你的任务 1 成果物已拿到，顺利完工！**


## 任务 2：修改 Apache 默认网页 (定制专属主页)

这个任务的目标是把 Apache 安装后自带的官方欢迎页，替换成咱们自己写的网页。

### 1. SSH 连入 Ubuntu 服务器
打开 Windows 的 `cmd` 终端，连接到你的 Ubuntu1 虚拟机：
```bash
ssh user@192.168.xxx.xxx
```
> **提示**：记得把 IP 换成你实际的 Ubuntu1 IP。

### 2. 备份默认网页
在修改任何系统核心文件前，先做一个备份。Linux 的默认网页存放在 `/var/www/html` 目录下。
```bash
# 切换到网页存放目录
cd /var/www/html

# 把原本的 index.html 复制一份，命名为 index.html.bak 作为备份
sudo cp index.html index.html.bak
```

### 3. 写入新的网页内容
我们将原有的网页删掉，用 `nano` 编辑器写个新的：
```bash
# 用管理员权限打开并编辑网页文件
sudo nano index.html
```

进入编辑器后，按住 `Ctrl+K` 把里面的老代码删干净，然后把下面这段简单的 HTML 代码复制粘贴进去：
```html
<!DOCTYPE html>
<html>
<head>
    <title>我的专属服务器</title>
    <meta charset="utf-8">
</head>
<body>
    <h1>欢迎来到 ldhan 的 Web 服务器！</h1>
    <p>这是我的 Linux 练习项目，任务 2 修改网页成功！</p>
</body>
</html>
```
> **保存退出操作**：
> 1. 按 `Ctrl + O` (保存)
> 2. 按 `Enter` (确认文件名)
> 3. 按 `Ctrl + X` (退出编辑器)

### 4. 验证与成果截图
1. 再次打开 Windows 上的浏览器。
2. 在地址栏输入你的虚拟机 IP：`http://192.168.xxx.xxx` 并回车。
3. 网页内容如果变成了 **"欢迎来到 ldhan 的 Web 服务器！"**，说明替换成功。
4. **立刻截图保存，这就是任务 2 的成果物！**

## 任务 3：配置 Proxy 跳板机与严格防火墙

目标：增加一台虚拟机 `Linux2` 作为跳板机（Proxy），设置 `Ubuntu1` 的防火墙，做到**只有 Linux2 能连上 Ubuntu1，Windows 宿主机直接连会超时**。

### 1. 准备跳板机 (Linux2)
1. 在 VMware 中按任务 1 的方法再建一台虚拟机，命名为 `Linux2`（网络同样选 NAT）。
2. 装好后进入 `Linux2` 终端，输入 `ip a` 获取它的 IP（假设为 `192.168.xxx.xxx`）。
3. 安装 SSH 客户端（以便从这里连别人）：
   ```bash
   sudo apt update && sudo apt install ssh -y
   ```

### 2. 配置 Ubuntu1 防火墙 (只给 Linux2 开门)
在 Windows `cmd` 连上 `Ubuntu1` (`ssh user@192.168.xxx.xxx`)，执行以下命令锁死大门：

```bash
# 1. 重置防火墙，清空之前的测试规则 (按 y 确认)
sudo ufw reset

# 2. 默认拒绝所有外部连接
sudo ufw default deny incoming

# 3. 添加白名单：只允许 Linux2 的 IP 访问 (请替换为实际的 Linux2 IP)
sudo ufw allow from 192.168.xxx.xxx

# 4. 重新启用防火墙
sudo ufw enable
```

### 3. 终极拦截：禁掉 Ping (ICMP)
Ubuntu 防火墙默认会放行 Ping 测试。为了达到完美的“请求超时”效果，需要修改底层配置文件：
```bash
sudo nano /etc/ufw/before.rules
```
找到下面这一行：
`-A ufw-before-input -p icmp --icmp-type echo-request -j ACCEPT`
把末尾的 `ACCEPT` 改成 `DROP`，按 `Ctrl+O` 回车保存，`Ctrl+X` 退出。
最后重新加载防火墙：
```bash
sudo ufw reload
```

### 4. 验证与成果截图
这是验证 Proxy 架构是否成功的关键对比测试：

#### 验证 A：Windows PC 直接访问失败 (截图 1)
1. 在 Windows 电脑打开 `cmd`，输入 `ping 192.168.xxx.xxx`。
2. 结果必须显示 **“请求超时” (Request timed out)**，代表防火墙成功把 PC 挡在门外。**（截图保存）**

#### 验证 B：通过 Proxy 访问成功 (截图 2)
1. 在 Windows `cmd` 先连入跳板机：`ssh user@192.168.xxx.xxx`
2. 成功进入 `Linux2` 后，在里面连入目标机：`ssh user@192.168.xxx.xxx`
3. 成功登入 `Ubuntu1`，终端提示符改变。这证明只有通过 Proxy 才能访问目标服务器。**（截图保存）**

## 任务 4：编写 Shell 脚本与 Cron 定时任务 (自动化运维)

目标：在 `Linux2` 上写一个脚本，自动抓取本机 IP 并带上时间戳保存到日志文件，然后用 Cron 做到定时自动执行。

### 1. 编写 IP 抓取脚本
在 `Linux2` 的终端中操作：
```bash
# 创建并编辑脚本文件
nano /home/user/ip_reader.sh
```
将以下代码复制粘贴进去：
```bash
#!/bin/bash
NOW=$(date +"%Y%m%d_%H%M%S")
hostname -I | awk '{print $1}' > "/home/ldhan/ip_check_${NOW}.log"
```
> **保存退出**：按 `Ctrl+O` -> 回车确认 -> `Ctrl+X` 退出。

### 2. 赋予执行权限
脚本写好了默认不能直接跑，得给它加个“可执行”权限：
```bash
chmod +x /home/ldhan/ip_reader.sh
```

### 3. 配置 Cron 定时任务
让系统自动定时跑这个脚本（这里以每 1 分钟执行一次为例，方便快速看效果）：
```bash
crontab -e
```
*(如果是第一次打开，输入 1 选择 nano 编辑器)*

在文件最末尾另起一行，加入以下内容：
```text
* * * * * /home/ldhan/ip_reader.sh
```
> **保存退出**：按 `Ctrl+O` -> 回车确认 -> `Ctrl+X` 退出。
*(注：`* * * * *` 代表每分钟执行一次。如果想按严格的 30 分钟，改成 `*/30 * * * *` 即可)*

### 4. 验证与成果截图
配置完等上 1 到 2 分钟，让定时任务后台跑一跑。

#### 验证 A：检查日志文件是否自动生成 (截图 1)
在终端输入：
```bash
ls -l /home/ldhan/ip_check_*.log
```
**预期结果**：能看到按当前时间命名的 `.log` 文件列表。**（截图保存）**

#### 验证 B：查看日志内容 (截图 2)
用 `cat` 命令查看其中一个文件的内容（请把下面的文件名替换成你实际查到的文件名）：
```bash
cat /home/ldhan/ip_check_20260304_153000.log
```
**预期结果**：终端会输出 `Linux2` 的 IP 地址。连同执行的命令和打印出来的 IP 一起**截图保存**。

🎉 **至此，所有 Linux 网络与自动化运维练习全部圆满完成！**
