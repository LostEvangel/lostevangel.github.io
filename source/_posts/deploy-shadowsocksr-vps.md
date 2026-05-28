---
title: VPS 部署 ShadowsocksR 实战
date: 2026-05-28 01:40:00
tags: [ShadowSocks, SSR, Linux, VPS]
categories: Linux
---

最近入手了一台 VPS，记录一下部署 SSR（ShadowsocksR）的完整过程，方便以后复盘。

<!-- more -->

## 准备工作

VPS 信息：

- OS：CentOS 9 (AlmaLinux 9)
- IP：自行购买
- 账户：root

## 1. 连接服务器

通过 SSH 连接到 VPS：

```bash
ssh root@<your-server-ip>
```

SSH 连接后输入密码即可登录。

## 2. 安装 Git

SSR 代码托管在 GitHub，需要先安装 git：

```bash
yum install -y git
```

## 3. 克隆 SSR 项目

SSR 的 manyuser 分支是目前广泛使用的版本，功能稳定，克隆到 `/home/work` 目录：

```bash
mkdir -p /home/work
cd /home/work
git clone -b manyuser https://github.com/LostEvangel/shadowsocksr.git
```

## 4. 初始化配置

进入项目目录，运行初始化脚本生成配置文件：

```bash
cd /home/work/shadowsocksr
bash initcfg.sh
```

执行后会在当前目录生成 `user-config.json`，这是单用户模式的核心配置文件。

## 5. 修改配置

编辑 `user-config.json`，填入服务器参数：

```json
{
    "server": "0.0.0.0",
    "server_port": 8888,
    "password": "your-password",
    "method": "aes-256-cfb",
    "protocol": "auth_aes128_md5",
    "obfs": "tls1.2_ticket_auth",
    "timeout": 120,
    "fast_open": false
}
```

参数说明：

| 参数 | 说明 |
|------|------|
| `server_port` | SSR 监听端口 |
| `password` | 连接密码 |
| `method` | 加密方式，推荐 aes-256-cfb |
| `protocol` | 协议插件，auth_aes128_md5 兼容性好 |
| `obfs` | 混淆插件，tls1.2_ticket_auth 伪装 TLS |

## 6. 启动 SSR

进入 `shadowsocks/` 子目录，使用单用户模式启动（读取父目录的 `user-config.json`）：

```bash
cd /home/work/shadowsocksr/shadowsocks
nohup python server.py >> /home/work/shadowsocksr/ssserver.log 2>&1 &
```

验证是否启动成功：

```bash
ss -lntp | grep python
```

看到 `LISTEN 0 1024 *:8888 *:*` 即表示运行中。

## 7. 防火墙配置

如果系统开启了 firewalld，需要放行端口：

```bash
firewall-cmd --zone=public --add-port=8888/tcp --permanent
firewall-cmd --reload
```

某些 VPS 厂商（如海外 VPS）默认不开启防火墙，可跳过此步。

## 客户端配置

下载对应平台的 SSR 客户端，填入以下参数即可连接：

| 配置项 | 值 |
|--------|-----|
| 服务器地址 | 你的 VPS IP |
| 端口 | 8888 |
| 密码 | 你设定的密码 |
| 加密 | aes-256-cfb |
| 协议 | auth_aes128_md5 |
| 混淆 | tls1.2_ticket_auth |

## 常用管理命令

```bash
# 查看日志
tail -f /home/work/shadowsocksr/ssserver.log

# 停止 SSR
pkill -f server.py

# 启动 SSR
cd /home/work/shadowsocksr/shadowsocks
nohup python server.py >> /home/work/shadowsocksr/ssserver.log 2>&1 &
```

## 注意事项

1. **`cymysql` 报错**：日志中出现 `ModuleNotFoundError: No module named 'cymysql'` 是正常的，这是多用户数据库模式才需要的依赖，单用户模式不会影响使用
2. **外网扫描**：VPS 上线后会遇到大量外网扫描流量，日志中出现 `unsupported addrtype` 是扫描器在试探，忽略即可
3. **Python 版本**：CentOS 9 自带 Python 3.9，SSR 原生兼容无需额外配置
4. **混淆选择**：`tls1.2_ticket_auth` 是精确匹配模式，如果客户端不支持可选 `tls1.2_ticket_auth_compatible` 提高兼容性
